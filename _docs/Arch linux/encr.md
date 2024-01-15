---
title: Arch linux with LUKS encryption 
category: Arch linux
order: 1
---

## Install Arch Linux

  Connect the USB drive and boot from the Arch Linux ISO.
  
  Make sure the system is booted in UEFI mode. The following command should display the directory contents without error.

    # ls /sys/firmware/efi/efivars
 
  Connect to the internet. A wired connection is preferred since it's easier to connect. More info

  Run fdisk to create Linux partitions.

    # fdisk /dev/<your-disk>

  If you have installed Windows, you already have a GPT partition table. Otherwise, create an empty GPT partition table using the g command. (WARNING: This will erase the entire disk.)

    # WARNING: This will erase the entire disk.

    Command (m for help): g
    Created a new GPT disklabel (GUID: ...).

   Create the EFI partition (/dev/<your-disk-efi>):

    Command (m for help): n
    Partition number: <Press Enter>
    First sector: <Press Enter>
    Last sector, +/-sectors or +/-size{K,M,G,T,P}: +100M

    Command (m for help): t
    Partition type or alias (type L to list all): uefi

   Create the Boot partition (/dev/<your-disk-boot>):

    Command (m for help): n
    Partition number: <Press Enter>
    First sector: <Press Enter>
    Last sector, +/-sectors or +/-size{K,M,G,T,P}: +512M

    Command (m for help): t
    Partition type or alias (type L to list all): linux

   Create the LUKS partition (/dev/<your-disk-luks>):

    Command (m for help): n
    Partition number: <Press Enter>
    First sector: <Press Enter>
    Last sector, +/-sectors or +/-size{K,M,G,T,P}: <Press Enter>

    Command (m for help): t
    Partition type or alias (type L to list all): linux

  Print the partition table using the p command and check that everything is OK:

    Command (m for help): p

  Write changes to the disk using the w command. (Make sure you know what you're doing before running this command).

    Command (m for help): w

  Format the EFI and Boot Partitions.

    mkfs.fat -F 32 /dev/<your-disk-efi>
    mkfs.ext4 /dev/<your-disk-boot>

  Set up the encrypted partition. You can choose any other name instead of cryptlvm.

    # cryptsetup --use-random luksFormat /dev/<your-disk-luks>
    # cryptsetup luksOpen /dev/<your-disk-luks> cryptlvm

  Create an LVM volume group. You can choose any other name instead of vg0.

    # pvcreate /dev/mapper/cryptlvm
    # vgcreate vg0 /dev/mapper/cryptlvm

  Create LVM partitions (logical volumes).

 We create logical volumes for swap, root (/), and home (/home). Leave 256MiB of free space in the volume group because the e2scrub command requires the LVM volume group to have at least 256MiB of unallocated space to dedicate to the snapshot.

    # lvcreate --size 8G vg0 --name swap
    # lvcreate --size 100G vg0 --name root
    # lvcreate -l +100%FREE vg0 --name home
    # vreduce --size -256M vg0/home

 Format logical volumes.

    # mkswap /dev/vg0/swap
    # mkfs.ext4 /dev/vg0/root
    # mkfs.ext4 /dev/vg0/home

 Mount new filesystems.

    # mount /dev/vg0/root /mnt
    # mount --mkdir /dev/<your-disk-efi> /mnt/efi
    # mount --mkdir /dev/<your-disk-boot> /mnt/boot
    # mount --mkdir /dev/vg0/home /mnt/home
    # swapon /dev/vg0/swap

 Install the base system. We also install some useful packages like git, vim, and sudo.

    # pacstrap -K /mnt base linux linux-firmware openssh git vim sudo

 Generate /etc/fstab. This file can be used to define how disk partitions, various other block devices, or remote filesystems should be mounted into the filesystem.

    # genfstab -U /mnt >> /mnt/etc/fstab

 Enter the new system.

    # arch-chroot /mnt /bin/bash

 Set TimeZone.

    ---> See available timezones:
    # ls /usr/share/zoneinfo/

    ---> Set timezone:
    # ln -s /usr/share/zoneinfo/Asia/Tehran /etc/localtime

 Run hwclock(8) to generate /etc/adjtime.

    # hwclock --systohc

 Set Locale.

    # vim /etc/locale.gen (uncomment en_US.UTF-8 UTF-8)
    # locale-gen
    # echo LANG=en_US.UTF-8 > /etc/locale.conf

 Set hostname.

    # echo yourhostname > /etc/hostname

 Create a user.

    # useradd -m -G wheel --shell /bin/bash yourusername
    # passwd yourusername
    # visudo
    ---> Uncomment "%wheel ALL=(ALL) ALL"

 Configure mkinitcpio with modules needed to create the initramfs image.

    # pacman -S lvm2
    # vim /etc/mkinitcpio.conf
    ---> Add 'encrypt' and 'lvm2' to HOOKS before 'filesystems'

 Recreate the initramfs image:

    # mkinitcpio -P

 Setup GRUB.

    # pacman -S grub efibootmgr
    # grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB

In /etc/default/grub edit the line GRUB_CMDLINE_LINUX as follows. Don't forget to replace /dev/<your-disk-luks> with the appropriate path.

    GRUB_CMDLINE_LINUX="cryptdevice=/dev/<your-disk-luks>:cryptlvm root=/dev/vg0/root"

If you have installed Windows and want to add Windows to the GRUB menu, edit /etc/grub.d/40_custom:

    #!/bin/sh
    exec tail -n +3 $0
    # This file provides an easy way to add custom menu entries.  Simply type the
    # menu entries you want to add after this comment.  Be careful not to change
    # the 'exec tail' line above.
    if [ "${grub_platform}" == "efi" ]; then
      menuentry "Windows 11" {
        insmod part_gpt
        insmod fat
        insmod search_fs_uuid
        insmod chain

        # After --set=root, add the Windows EFI partition's UUID.
        # (can be found with "blkid" command)
        search --fs-uuid --set=root $FS_UUID
        chainloader /EFI/Boot/bootx64.efi
      }
    fi

In the above script, replace $FS_UUID with Windows EFI partition UUID. You can find this UUID using lsblk command. It should be something like 8E12-69DD.

Now generate the main GRUB configuration file:

    # grub-mkconfig -o /boot/grub/grub.cfg

Install networkmanager package and enable NetworkManager service to ensure you have Internet connectivity after rebooting.

    # pacman -S networkmanager
    # systemctl enable NetworkManager

Exit new system and unmount all filesystems.

    # exit
    # umount -R /mnt
    # swapoff -a

Arch is now installed. Reboot.

    # reboot

Notes
Backup LUKS Header

It is important to make a backup of LUKS header so that you can access your data in case of emergency (if your LUKS header somehow gets damaged).

Create a backup file:

    # cryptsetup luksHeaderBackup /dev/<your-disk-luks> --header-backup-file luks-header-backup-$(date -I)

Store the backup file in a safe place, such as a USB drive. If something bad happens, you can restore the backup header:

    # cryptsetup luksHeaderRestore /dev/<your-disk-luks> --header-backup-file /path/to/backup_header_file



