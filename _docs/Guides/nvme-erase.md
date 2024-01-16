---
title: NVMe Secure Erase 
category: Guides
order: 4
---
#### nvme-format, part of nvme-cli (NVMe management command line interface), offers two Secure Erase options:

Secure Erase Settings: This field specifies whether a secure erase should be performed as part of the format and the type of the secure erase operation. 
The erase applies to all user data, regardless of location (e.g., within an exposed LBA, within a cache, within deallocated LBAs, etc). Defaults to 0.



| Value | Definition                                                                                                           |
|-------|-----------------------------------------------------------------------------------------------------------------------|
| 0     | No secure erase operation requested                                                                                  |
| 1     | User Data Erase: All user data shall be erased, contents of the user data after the erase is indeterminate. The controller may perform a cryptographic erase when a User Data Erase is requested if all user data is encrypted. |
| 2     | Cryptographic Erase: All user data shall be erased cryptographically. This is accomplished by deleting the encryption key.|
| 3-7   | Reserved                                                                                                             |

[NVM Express Base Specification Revision 1.3c May 24](https://nvmexpress.org/wp-content/uploads/NVM-Express-1_3c-2018.05.24-Ratified.pdf), 2018 states:

As part of the Format NVM command, the host may request a secure erase of the contents of the NVM. 
There are two types of secure erase. The User Data Erase erases all user content present in the NVM subsystem. 
The Cryptographic Erase erases all user content present in the NVM subsystem by deleting the encryption key with which the user data was previously encrypted.

#### Usage

#### 0. Install ```nvme-cli```
The nvme-cli [README.md](https://github.com/linux-nvme/nvme-cli/blob/master/README.md) includes installation instructions for a number of Linux distributions,
though building is as simple as ```make && make install```

#### 1. List NVMe devices

    # nvme list

      Node             SN                   Model                                    Namespace Usage                      Format           FW Rev  
      ---------------- -------------------- ---------------------------------------- --------- -------------------------- ---------------- --------
     /dev/nvme0n1     XXXXXXXXXXXX         WDS250G2X0C-00L350                       1         250.06  GB / 250.06  GB    512   B +  0 B   101110WD

#### 2. Verify Secure Erase support
   
    # nvme id-ctrl -H /dev/nvme0
 
       NVME Identify Controller:
       vid     : 0x15b7
       ssvid   : 0x15b7
       sn      : XXXXXXXXXXXX        
       mn      : WDS250G2X0C-00L350                      
       fr      : 101110WD
       ...
       oacs    : 0x17
         [1:1] : 0x1 Format NVM Supported
       ...
       fna     : 0
         [2:2] : 0 Crypto Erase Not Supported as part of Secure Erase
       ...
      
      
  This device supports User Data Erase (value 1 in above table), but not Cryptographic Erase (value 2 in above table).


#### 3. Issue Secure Erase command

    # nvme format /dev/nvme0 --ses=1
    Success formatting namespace:ffffffff

#### 4. If errors were encountered, suspend and resume computer then re-run

    # nvme format /dev/nvme0 --ses=1
    NVME Admin command error:INVALID_FORMAT(410a)
    # systemctl -i suspend
    # nvme format /dev/nvme0 --ses=1
    Success formatting namespace:ffffffff

#### 5. Verify

    # hexdump /dev/nvme0n1
    0000000 0000 0000 0000 0000 0000 0000 0000 0000
    *
    3a38b2e000

#### Notes

- There are [reports](https://github.com/linux-nvme/nvme-cli/issues/84) of "NVME Admin command error:INVALID_OPCODE(2001)" on Samsung NVMe SSDs being resolved by suspending and waking the computer as well.
- The [manpage](http://manpages.ubuntu.com/manpages/bionic/en/man1/nvme-format.1.html) on specifying the character device (e.g., /dev/nvme0) vs the namespace block device (e.g., /dev/nvme0n1): If the character device is given,
  the namespace identifier will default to 0xffffffff to send the format to all namespace, but can be overridden to any namespace with the 'namespace-id' option.
  If the block device is given, the namespace identifier will default to the namespace id of the block device given, but can be overridden with the same option.
- For more information on NVMe character devices (e.g., /dev/nvme0) vs. namespace block devices (e.g., /dev/nvme0n1), see [Why is there both character device and
  block device for nvme?](https://serverfault.com/questions/892134/why-is-there-both-character-device-and-block-device-for-nvme) and [Managing NVMe Namespaces.](https://narasimhan-v.github.io/2020/06/12/Managing-NVMe-Namespaces.html)



Credits: [z399](https://github.com/z399)


