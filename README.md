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
2. sudo apt install make git build-essential zlib1g-dev gcc cvs wget -y
3. wget https://github.com/bringlive/OpenTracker/releases/download/Data/libowfat.tar.gz && tar -zxvf libowfat.tar.gz && cd libowfat && make
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
6. Then test your build with: ./opentracker &
    <img width="729" height="129" alt="Screenshot at 2026-01-21 09-27-09" src="https://github.com/user-attachments/assets/65a6ab17-bdcc-4c6a-8d1f-9a41bb9eaddd" />
    
Opentracker uses port 6969 by default. You can access it in any web browser as follows:
- For example: http://server_ip:6969/stats
     ![opentracker-debian-1-e1681734914369-768x209](https://github.com/user-attachments/assets/9eed2734-7f27-4c25-b45f-c7e9e381d471)
  
  To stop OpenTracker, you must kill the process id it shows at startup:
     - kill -9 2465

7. Limit access to Opentracker
   - If you want to limit access to Opentracker to a certain IP and port, start it as follows:
     - ./opentracker.debug -i 192.168.122.177 -p 6969
       - Binding socket type TCP to address [192.168.122.177]:6969... success. Installing 0 workers on udp socket -1

8. Create OpenTracker Service File
   - It can be difficult to always run OpenTracker from the relevant directory. Now let's turn it into a service.
First move the app to the opt directory:
     - sudo mv opentracker /opt/
     - Then create a service file:
     - sudo nano /etc/systemd/system/opentracker.service
     - Then copy the following content into it:
          '''
         > [Unit]
         
         > Description=opentracker Service
         
         > After=network.target

         > [Service]
         
         > Type=simple
         
         > ExecStart=/opt/opentracker/opentracker &
         
         > Restart=on-failure

         > [Install]
         
         > WantedBy=multi-user.target"
         '''
     - Then reload the service file:
         - sudo systemctl daemon-reload
     - Enable it:
         - sudo systemctl enable opentracker.service
          > Created symlink /etc/systemd/system/multi-user.target.wants/opentracker.service → /etc/systemd/system/opentracker.service.
     - And start it again:
         - sudo systemctl start opentracker.service
     - Look at the service status and you will see it running:
         - sudo systemctl status opentracker.service
           > ● opentracker.service - opentracker Service
   Loaded: loaded (/etc/systemd/system/opentracker.service; enabled; ven
   Active: active (running) since Tue 2023-04-11 22:13:43 +03; 6s ago
 Main PID: 1452 (opentracker)
    Tasks: 4 (limit: 2319)
   Memory: 460.0K
   CGroup: /system.slice/opentracker.service
           └─1452 /opt/opentracker/opentracker &
           >Apr 11 22:13:43 pardus systemd[1]: Started opentracker Service.
           
      - OpenTracker default configurations are in the opentracker.conf file. Copy a sample file:
        - sudo cp /opt/opentracker/opentracker.conf.sample /opt/opentracker/opentracker.conf
      - You can edit as you wish:
        - sudo nano /opt/opentracker/opentracker.conf
      - A service restart may be required after changes. To stop the service, you can write:
        - sudo systemctl stop opentracker.service
       

## Invocation
opentracker can be run by just typing ./opentracker. This will make opentracker bind to 0.0.0.0:6969 and [::1]:6969 and happily serve all torrents presented to it. If ran as root, opentracker will immediately chroot to . (or any directory given with the -d option) and drop all priviliges after binding to whatever tcp or udp ports it is requested.

When options were few, opentracker used to accept all of them from command line. While this still is possible for most options, using them is quite unhandy: an example invocation would look like ./opentracker -i 23.23.23.7 -p 80 -P 80 -p 6969 -i 23.23.23.8 -p 80 -r http://www.mytorrentsite.com/ -d /usr/local/etc/opentracker -w mytorrents.list -A 127.0.0.1.

opentracker now uses a config file that you can provide with the -f switch.


## Closed mode
While personally I like my tracker to be open, I can see that there's people that want to control what torrents to track – or not to track. If you've compiled opentracker with one of the accesslist-options (see Build instructions above), you can control which torrents are tracked by providing a file that contains a list of human readable info_hashes. An example whitelist file would look like

0123456789abcdef0123456789abcdef01234567
890123456789abcdef0123456789abcdef012345
To make opentracker reload it's white/blacklist, send a SIGHUP unix signal.


## Statistics
Given its very network centric approach, talking to opentracker via http comes very naturally. Besides the /announce and /scrape paths, there is a third path you can access the tracker by: /stats. This request takes parameters, for a quick overview just inquire /stats?mode=everything.

Statistics have grown over time and are currently not very tidied up. Most modes were written to dump legacy-SNMP-style blocks that can easily be monitored by MRTG. These modes are: peer, conn, scrp, udp4, tcp4, busy, torr, fscr, completed, syncs. I'm not going to explain these here.

The statedump mode dumps non-recreatable states of the tracker so you can later reconstruct an opentracker session with the -l option. This is beta and wildly undocumented.

You can inquire opentracker's version (i.e. CVS versions of all its objects) using the version mode.
