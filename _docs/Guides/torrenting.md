---
title: Introdcution to Torrenting
category: Guides
order: 2
---


# Torrenting

Torrenting is a peer-to-peer file-sharing method that facilitates the distribution and downloading of files over the internet.

Unlike traditional file downloads, where files are obtained from a single source, torrenting leverages the collective resources of numerous participants, known as peers. This collaborative process enhances download speeds and reduces strain on individual servers, providing an efficient and resilient method for file transferring. A [torrent client](#torrent-client) allows you to connect with these peers and download the file in small fragments, which are put together to create the final result.

## Understanding the Terminology

Term               | Definition
-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Leecher**        | A user who is currently downloading the file, or has finished downloading some parts of the torrent and is sharing the pieces they have
**Magnet link**    | A BitTorrent hyperlink that allows you to download the torrent without the need for a `.torrent` file
**Peer**           | An individual computer or device connected to the swarm
**Piece**          | A small segment of the file(s) that was cut up during the torrent creation process, allowing simultaneous downloading and uploading of different pieces across peers
**Ratio**          | The ratio between the amount of data uploaded and downloaded by the user
**Seeder**         | A user who has completed downloading the files and is sharing them with other peers by uploading the file pieces
**Swarm**          | The collective group of peers (seeders and leechers) distributing the files
**Torrent client** | The software or application used to download and upload files using the BitTorrent protocol
**Torrent file**   | A file (`.torrent`) that contains metadata about the files to be shared and the tracker information required to initiate and coordinate the downloading process
**Tracker**        | A server or web service that assists in coordinating the communication between peers in a BitTorrent network by keeping track of which peers possess which pieces of a file



For most people, streaming services are enough and more convenient to use. However, torrenting is fairly simple and allows for better flexibility. Some reasons to torrent include:

- Broad range of qualities and sizes
- Significantly better quality compared to streaming sites
- Better and faster availability
- Better subtitles/fansubs and styling options
- Access to a larger variety of titles and Blu-ray releases
- Downloaded files for rewatching with no additional data usage

**WARNING** *Downloading torrents may be illegal depending on where you live. To avoid receiving a copyright infringement notice from your ISP, you may want to consider using a VPN or seedbox !!!.*

## Torrent Client

To start torrenting, you'll need a torrent client. This is the software that you will use to download torrents. Below is a list of some popular clients:

- [qBittorrent](https://www.qbittorrent.org) recommended for pc
- [Deluge](https://deluge-torrent.org/)
- [LibreTorrent](https://github.com/proninyaroslav/libretorrent) recommended for android
- [rTorrent](https://github.com/rakshasa/rtorrent) 
- [Transmission](https://transmissionbt.com/)

For most users, we recommend using qBittorrent. It's a free, open-source, and feature-full client that is easy to use and understand.

**WARNING** *You should not use newer versions of [μTorrent](https://wikipedia.org/wiki/%CE%9CTorrent) or [BitTorrent](https://wikipedia.org/wiki/BitTorrent_(software)), as they are known to be bundled with malware and adware.*


## Getting Torrents

Torrents are shared using `.torrent` files or magnet links, which contain the necessary metadata of the file to be downloaded. These can be obtained through various trackers.

`.torrent` files can be added to your torrent client by opening it or browsing for the file manually. Magnet links can be opened in your browser, where it will prompt you to choose the torrent client to be opened with. Alternatively, you can paste this magnet link into your client.


*See the list of [public trackers](https://rentry.co/megathread-all-purpose#torrents) or [private trackers](https://hdvinnie.github.io/Private-Trackers-Spreadsheet/) for places to find torrents.*

## Additional Tools

### Port Forwarding

Port forwarding allows computers on other networks to be able to access services behind your network. Opening ports gives your client better connectability, enabling you to download from seeders with closed ports and upload to leechers with closed ports much faster.

### Enabling port forwarding

By default, most torrent clients automatically try to forward the current port via UPnP. You can test if this is working by using a port checker such as [CanYouSeeMe.org](https://canyouseeme.org) and entering the port.

If UPnP doesn't work, you can try manually forwarding the port through your router settings.

**WARNING**
*The port forwarding tutorial below is intended for those using home routers. This will not work with VPNs. Check with your provider to see if port forwarding is available.*

- Find the port used by your client. In qBittorrent, this can be found under **Tools** -> **Options** -> **Connection** -> **Listening Port**
- Access your router's default gateway in your browser. You can find this on Windows by running Command Prompt and typing in `ipconfig`. Some common examples are:
  - [`192.168.0.1`](http://192.168.0.1)
  - [`172.16.0.1`](http://172.16.0.1)
  - [`10.0.0.1`](http://10.0.0.1)
- Locate the port forwarding option for your router. *We recommend using ports from [49152 or higher](https://wikipedia.org/wiki/Ephemeral_port) to avoid interference with other services as well as being blocked by your ISP, which is common on lower ports*
- After saving/restarting your router and torrent client, go to [CanYouSeeMe.org](https://canyouseeme.org) to check if the port is now open
  - If the port is closed, then you are likely using a NAT. *Unfortunately, this means you will need to use a different ISP or use a VPN that supports port forwarding*

**DANGER** *Do not enable all ports, as they may interfere with other services or put you at risk. Avoid enabling any [well-known ports](https://wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers#Well-known_ports) and [registered ports](https://wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers#Registered_ports).*


### VPN

A VPN is an encrypted network tunnel between your device and the internet, allowing you to mask your activity from your ISP. In the case of torrenting, this allows you to hide your torrent traffic and prevent someone from trying to spy on you.

Depending on which country you live in, your ISP may send you a copyright infringement notice, threaten to shut off your internet, or do absolutely nothing. On a good VPN service, this will never reach your ISP, as it is blocked by the VPN protecting your activity. *If your country is strict about it, you should highly consider buying a VPN, such as in North America or Europe.*

**WARNING** *Do not use free VPNs. These services often have limitations, weak security, and questionable privacy concerns. These may put you at greater risk compared to a premium service.*

*If you use qBittorrent, you may want to consider binding it to your client*


#### Forwarding Ports

Some VPNs offer the ability to forward ports, which is especially useful if you are not able to port forward normally as it bypasses ISP restrictions:

- [AirVPN](https://airvpn.org) - Offers 5 static ports. *Recommended to use with a third-party client such as [WireSock](https://github.com/wiresock/WireSockUI)*

- [ProtonVPN](https://protonvpn.com) - Offers 1 dynamic port. *Recommended to use with an [automatic port mapping client](https://github.com/ravesheep/ProtonVPN-windows) to avoid manually updating ports on reconnect*

#### Split Tunneling

Split tunneling allows you to selectively route your internet traffic through the VPN tunnel or your internet connection simultaneously. With split tunneling, you can choose specific applications, websites, or services to be routed through the VPN while allowing other traffic to bypass the VPN and use the regular internet connection.

*See the [split tunneling guide for Wireguard](https://help.rapidseedbox.com/en/articles/6810079-split-tunneling-with-wireguard-on-android) on how to set it up.*

### Seedbox

A seedbox is a dedicated server optimized for high-speed downloading and seeding of torrents. Unlike VPNs, seedboxes are separate from your network, allowing for better peace of mind when torrenting.

Other advantages include:

- Better uptime as it doesn't require your PC to run. *This is especially useful if you plan to build ratio on various trackers*
- Higher prioritization when seeding due to their high speed
- Avoiding copyright notices, as they are directed to your box instead (i.e. ignored)
- Synergy and integration with third-party applications/plugins (e.g. media servers)

Seedboxes can be run on your own virtual private server (VPS) or personal computer. Alternatively, they can be hosted through various seedbox providers.

Credits: [z399](https://github.com/z399)

