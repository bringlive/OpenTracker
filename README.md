# OpenTracker

### An open and free bittorrent tracker 

<img width="601" height="398" alt="opentracker_beta3" src="https://github.com/user-attachments/assets/0587d1ac-f009-47c9-b983-f2a15d14d087" />


## Overview
opentracker is an open and free [bittorrent tracker](http://wiki.theory.org/BitTorrentSpecification) [project](https://erdgeist.org/arts/software/opentracker/). It aims for minimal resource usage and is intended to run at your wlan router. Currently it is deployed as an open and free tracker instance. Read our free and open tracker blog and announce your torrents there (but do not hesitate to setup your own free trackers!).

## Version History
Use git clone git://erdgeist.org/opentracker to check it out. An opentracker [gitweb](https://erdgeist.org/gitweb/opentracker/) is available.

CURRENT - not packaged yet

V1.0 [opentracker-1.0](https://erdgeist.org/arts/software/opentracker/opentracker-1.0.tar.bz2) (2025-01-01)

After 18 years of development, a v1.0 was released.

It requires [libowfat](http://www.fefe.de/libowfat/) version >= 0.34.

Since April 2024, opentracker supports IPv4 and IPv6 peers in a single tracker.

With TCP announces, both IPv4 and IPv6 peers will now be returned.

Proxy addresses can be networks in CIDR notation now.

For trackers with many insertions and deletions in black/white lists, dynamic access lists were introduced, with fifo allowing to add or delete torrents.

Full scrapes and other data intense queries are now delivered using chunked http transfers, allowing generating them on the fly, saving a lot of memory. Also they are compressed by default now.

UDP replies on the IPv6 side are now limited to amounts of peers that will not cause fragmentation on most routers.

Better prevent expensive stats operations to be triggered by accident. Some torrents included a stats URL as announce URL.


## Build instructions
Until opentracker is declared official release ready, the way to install it is:
1. sudo apt update && sudo apt upgrade -y
2. sudo apt install make git build-essential zlib1g-dev gcc cvs curl
3. curl -L http://www.fefe.de/libowfat/libowfat-0.34.tar.xz -o libowfat && cd libowfat && make
4. cd ..
5. git clone git://erdgeist.org/opentracker && cd opentracker && make
6. Then test your build with: ./opentracker &
    
