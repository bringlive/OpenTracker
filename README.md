# OpenTracker

### An open and free bittorrent tracker 

<img width="601" height="398" alt="opentracker_beta3" src="https://github.com/user-attachments/assets/0587d1ac-f009-47c9-b983-f2a15d14d087" />


## Overview
opentracker is an open and free [bittorrent tracker](http://wiki.theory.org/BitTorrentSpecification) [project](https://erdgeist.org/arts/software/opentracker/). It aims for minimal resource usage and is intended to run at your wlan router. Currently it is deployed as an open and free tracker instance. Read our free and open tracker blog and announce your torrents there (but do not hesitate to setup your own free trackers!).

## Version History
Use git clone git://erdgeist.org/opentracker to check it out. An opentracker [gitweb](https://erdgeist.org/gitweb/opentracker/) is available.

CURRENT - not packaged yet

V1.0 [opentracker](https://erdgeist.org/arts/software/opentracker/opentracker-1.0.tar.bz2) (2025-01-01)

- After 18 years of development, a v1.0 was released.

- It requires [libowfat](http://www.fefe.de/libowfat/) version >= 0.34.

- Since April 2024, opentracker supports IPv4 and IPv6 peers in a single tracker.

- With TCP announces, both IPv4 and IPv6 peers will now be returned.

- Proxy addresses can be networks in CIDR notation now.

- For trackers with many insertions and deletions in black/white lists, dynamic access lists were introduced, with fifo allowing to add or delete torrents.

- Full scrapes and other data intense queries are now delivered using chunked http transfers, allowing generating them on the fly, saving a lot of memory. Also they are compressed by default now.

- UDP replies on the IPv6 side are now limited to amounts of peers that will not cause fragmentation on most routers.

- Better prevent expensive stats operations to be triggered by accident. Some torrents included a stats URL as announce URL.


## Build instructions
Until opentracker is declared official release ready, the way to install it is:
1. sudo apt update && sudo apt upgrade -y
2. sudo apt install make git build-essential zlib1g-dev gcc cvs curl
3. curl -L http://www.fefe.de/libowfat/libowfat-0.34.tar.xz -o libowfat && cd libowfat && make
4. cd ..
5. git clone git://erdgeist.org/opentracker && cd opentracker && make
   - That should leave you with an exectuable called opentracker and one debug version opentracker.debug
   - Some variables in opentracker's Makefile control features and behaviour of opentracker. Here they are:
     - -DWANT_V4_ONLY makes opentracker bind only on IPv4 addresses.
     - opentracker can deliver gzip compressed full scrapes. Enable this with -DWANT_COMPRESSION_GZIP option (this is the default).
       - To make opentracker deliver gzip compressed full scrapes always, enable the -DWANT_COMPRESSION_GZIP_ALWAY option (this is the default).
     - Normally opentracker tracks any torrent announced to it. You can change that behaviour by enabling ONE of -DWANT_ACCESSLIST_BLACK or -DWANT_ACCESSLIST_WHITE. Note, that you have to provide a whitelist file in order to make opentracker do anything in the latter case. More in the closed mode section below.
     - opentracker can run in a cluster. Enable this behaviour by enabling -DWANT_SYNC_LIVE. Note, that you have to configure your cluster before you can use opentracker when this option is on.
     - Some statistics opentracker can provide are sensitive. You can restrict access to these statistics by enabling -DWANT_RESTRICT_STATS. See section statistics for more details.
     - Fullscrapes is bittorrent's way to query a tracker for all tracked torrents. Since it's in the standard, it is enabled by default. Disable it by commenting out -DWANT_FULLSCRAPE.
     - By default opentracker will only allow the connecting endpoint's IP address to be announced. Bittorrent standard allows clients to provide an IP address in its query string. You can make opentracker use this IP address by enabling -DWANT_IP_FROM_QUERY_STRING.
     - Some experimental or older, deprecated features can be enabled by the -DWANT_LOG_NETWORKS, -DWANT_SYNC_SCRAPE or -DWANT_IP_FROM_PROXY switch.
     - Currently there is some packages for some linux distributions and OpenBSD around, but some of them patch Makefile and default config to make opentracker closed by default. I explicitly don't endorse those packages and will not give support for problems stemming from these missconfigurations.
7. Then test your build with: ./opentracker &
    <img width="729" height="129" alt="Screenshot at 2026-01-21 09-27-09" src="https://github.com/user-attachments/assets/65a6ab17-bdcc-4c6a-8d1f-9a41bb9eaddd" />
    
Opentracker uses port 6969 by default. You can access it in any web browser as follows:
- For example: http://server_ip:6969/stats
     ![opentracker-debian-1-e1681734914369-768x209](https://github.com/user-attachments/assets/9eed2734-7f27-4c25-b45f-c7e9e381d471)
  
  To stop OpenTracker, you must kill the process id it shows at startup:
     - kill -9 2465

