---
layout: post
title: Redirecting Personal Weather Station packets on Ubiquiti Unifi Network to database
date: 2021-01-27 +13:00
published: false
category: Smart Home
tags: [networking, smart home, ubiquiti, unifi, weather]
---


```
// TODO write this post
```


Device UAP-AC-Lite:3721: Connected


BusyBox v1.25.1 () built-in shell (ash)


  ___ ___      .__________.__
 |   |   |____ |__\_  ____/__|
 |   |   /    \|  ||  __) |  |   (c) 2010-2020
 |   |  |   |  \  ||  \   |  |   Ubiquiti Networks, Inc.
 |______|___|  /__||__/   |__|
            |_/                  https://www.ui.com/

      Welcome to UniFi UAP-AC-Lite!

UBNT-BZ.v4.3.24# tspdump host 192.168.1.102
/bin/sh: tspdump: not found
UBNT-BZ.v4.3.24# tcpdump host 192.168.1.102
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
20:26:31.219235 IP 192.168.1.102.57461 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [S], seq 1623858405, win 5840, options [mss 1460], length 0
20:26:31.448699 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57461: Flags [S.], seq 2559205795, ack 1623858406, win 65535, options [mss 1440], length 0
20:26:31.471398 IP 192.168.1.102.57461 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 1, win 5840, length 0
20:26:31.567645 IP 192.168.1.102.57461 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [P.], seq 1:351, ack 1, win 5840, length 350: HTTP: GET /weatherstation/updateweatherstation.php?ID=PWS_ID&PASSWORD=PWS_PASS&action=updateraww&realtime=1&rtfreq=5&dateutc=now&baromin=29.91&tempf=80.4&dewptf=42.4&humidity=26&windspeedmph=7.3&windgustmph=7.8&winddir=202&rainin=0.0&dailyrainin=0.0&indoortempf=81.1&indoorhumidity=24 HTTP/1.1
20:26:31.797800 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57461: Flags [.], ack 351, win 65535, length 0
20:26:31.805772 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57461: Flags [P.], seq 1:286, ack 351, win 65535, length 285: HTTP: HTTP/1.1 200 OK
20:26:31.942242 IP 192.168.1.102.57461 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 286, win 5555, length 0
20:26:32.072952 IP 192.168.1.102.57461 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [F.], seq 351, ack 286, win 5555, length 0
20:26:32.302422 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57461: Flags [F.], seq 286, ack 352, win 65535, length 0
20:26:32.303631 IP 192.168.1.102.57461 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 287, win 5554, length 0
20:26:43.219150 IP 192.168.1.102.57462 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [S], seq 1624249365, win 5840, options [mss 1460], length 0
20:26:43.448434 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57462: Flags [S.], seq 199345091, ack 1624249366, win 65535, options [mss 1440], length 0
20:26:43.546934 IP 192.168.1.102.57462 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 1, win 5840, length 0
20:26:43.645942 IP 192.168.1.102.57462 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [P.], seq 1:351, ack 1, win 5840, length 350: HTTP: GET /weatherstation/updateweatherstation.php?ID=PWS_ID&PASSWORD=PWS_PASS&action=updateraww&realtime=1&rtfreq=5&dateutc=now&baromin=29.91&tempf=80.4&dewptf=42.4&humidity=26&windspeedmph=8.9&windgustmph=9.8&winddir=158&rainin=0.0&dailyrainin=0.0&indoortempf=81.1&indoorhumidity=24 HTTP/1.1
20:26:43.876212 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57462: Flags [.], ack 351, win 65535, length 0
20:26:43.882182 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57462: Flags [P.], seq 1:286, ack 351, win 65535, length 285: HTTP: HTTP/1.1 200 OK
20:26:44.146772 IP 192.168.1.102.57462 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [F.], seq 351, ack 286, win 5555, length 0
20:26:44.376086 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57462: Flags [F.], seq 286, ack 352, win 65535, length 0
20:26:44.377657 IP 192.168.1.102.57462 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 287, win 5554, length 0
20:26:48.448717 ARP, Request who-has 192.168.1.102 tell unifi.localdomain, length 46
20:26:48.459798 ARP, Reply 192.168.1.102 is-at 34:00:8a:e2:3c:46 (oui Unknown), length 46
20:26:55.219255 IP 192.168.1.102.57463 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [S], seq 1624640373, win 5840, options [mss 1460], length 0
20:26:55.429830 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57463: Flags [S.], seq 2823843761, ack 1624640374, win 65535, options [mss 1440], length 0
20:26:55.529992 IP 192.168.1.102.57463 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 1, win 5840, length 0
20:26:55.616954 IP 192.168.1.102.57463 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [P.], seq 1:351, ack 1, win 5840, length 350: HTTP: GET /weatherstation/updateweatherstation.php?ID=PWS_ID&PASSWORD=PWS_PASS&action=updateraww&realtime=1&rtfreq=5&dateutc=now&baromin=29.91&tempf=80.4&dewptf=42.4&humidity=26&windspeedmph=6.2&windgustmph=6.4&winddir=225&rainin=0.0&dailyrainin=0.0&indoortempf=81.1&indoorhumidity=24 HTTP/1.1
20:26:55.828504 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57463: Flags [.], ack 351, win 65535, length 0
20:26:55.831388 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57463: Flags [P.], seq 1:286, ack 351, win 65535, length 285: HTTP: HTTP/1.1 200 OK
20:26:55.948895 IP 192.168.1.102.57463 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 286, win 5555, length 0
20:26:56.125785 IP 192.168.1.102.57463 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [F.], seq 351, ack 286, win 5555, length 0
20:26:56.336430 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57463: Flags [F.], seq 286, ack 352, win 65535, length 0
20:26:56.337930 IP 192.168.1.102.57463 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 287, win 5554, length 0
20:27:07.219409 IP 192.168.1.102.57464 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [S], seq 1625031429, win 5840, options [mss 1460], length 0
20:27:07.440071 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57464: Flags [S.], seq 1868272981, ack 1625031430, win 65535, options [mss 1440], length 0
20:27:07.492898 IP 192.168.1.102.57464 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 1, win 5840, length 0
20:27:07.592206 IP 192.168.1.102.57464 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [P.], seq 1:351, ack 1, win 5840, length 350: HTTP: GET /weatherstation/updateweatherstation.php?ID=PWS_ID&PASSWORD=PWS_PASS&action=updateraww&realtime=1&rtfreq=5&dateutc=now&baromin=29.91&tempf=80.2&dewptf=42.2&humidity=26&windspeedmph=6.2&windgustmph=6.4&winddir=225&rainin=0.0&dailyrainin=0.0&indoortempf=80.9&indoorhumidity=24 HTTP/1.1
20:27:07.813222 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57464: Flags [.], ack 351, win 65535, length 0
20:27:07.821409 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57464: Flags [P.], seq 1:286, ack 351, win 65535, length 285: HTTP: HTTP/1.1 200 OK
20:27:07.951166 IP 192.168.1.102.57464 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 286, win 5555, length 0
20:27:08.093728 IP 192.168.1.102.57464 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [F.], seq 351, ack 286, win 5555, length 0
20:27:08.314056 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57464: Flags [F.], seq 286, ack 352, win 65535, length 0
20:27:08.315276 IP 192.168.1.102.57464 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 287, win 5554, length 0
20:27:13.146165 IP 192.168.1.102.57465 > 5.175.47.13.80: Flags [S], seq 1625422507, win 5840, options [mss 1460], length 0
20:27:13.434144 IP 5.175.47.13.80 > 192.168.1.102.57465: Flags [S.], seq 689039923, ack 1625422508, win 29200, options [mss 1452], length 0
20:27:13.531842 IP 192.168.1.102.57465 > 5.175.47.13.80: Flags [.], ack 1, win 5840, length 0
20:27:13.590442 IP 192.168.1.102.57465 > 5.175.47.13.80: Flags [P.], seq 1:204, ack 1, win 5840, length 203: HTTP: GET /v01/set/wid//key//bar/10137/wdir/225/wspd/28/wspdhi/29/rainrate/0/rain/0/temp/268/chill/268/hum/26/heat/261/dew/57/tempin/272/humin/24 HTTP/1.1
20:27:13.878964 IP 5.175.47.13.80 > 192.168.1.102.57465: Flags [.], ack 204, win 30016, length 0
20:27:13.880026 IP 5.175.47.13.80 > 192.168.1.102.57465: Flags [P.], seq 1:173, ack 204, win 30016, length 172: HTTP: HTTP/1.1 401 Unauthorized
20:27:13.955014 IP 192.168.1.102.57465 > 5.175.47.13.80: Flags [.], ack 173, win 5668, length 0
20:27:14.104583 IP 192.168.1.102.57465 > 5.175.47.13.80: Flags [F.], seq 204, ack 173, win 5668, length 0
20:27:14.391874 IP 5.175.47.13.80 > 192.168.1.102.57465: Flags [F.], seq 173, ack 205, win 30016, length 0
20:27:14.393063 IP 192.168.1.102.57465 > 5.175.47.13.80: Flags [.], ack 174, win 5667, length 0
20:27:19.220522 IP 192.168.1.102.57466 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [S], seq 1625813611, win 5840, options [mss 1460], length 0
20:27:19.438717 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57466: Flags [S.], seq 1793426965, ack 1625813612, win 65535, options [mss 1440], length 0
20:27:19.466129 IP 192.168.1.102.57466 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 1, win 5840, length 0
20:27:19.572999 IP 192.168.1.102.57466 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [P.], seq 1:351, ack 1, win 5840, length 350: HTTP: GET /weatherstation/updateweatherstation.php?ID=PWS_ID&PASSWORD=PWS_PASS&action=updateraww&realtime=1&rtfreq=5&dateutc=now&baromin=29.91&tempf=80.2&dewptf=42.2&humidity=26&windspeedmph=5.3&windgustmph=5.3&winddir=292&rainin=0.0&dailyrainin=0.0&indoortempf=80.9&indoorhumidity=24 HTTP/1.1
20:27:19.791699 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57466: Flags [.], ack 351, win 65535, length 0
20:27:19.801090 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57466: Flags [P.], seq 1:198, ack 351, win 65535, length 197: HTTP: HTTP/1.1 401 Unauthorized
20:27:19.954352 IP 192.168.1.102.57466 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 198, win 5643, length 0
20:27:20.046041 IP 192.168.1.102.57466 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [F.], seq 351, ack 198, win 5643, length 0
20:27:20.263804 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57466: Flags [F.], seq 198, ack 352, win 65535, length 0
20:27:20.264964 IP 192.168.1.102.57466 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 199, win 5642, length 0
20:27:31.159463 IP 192.168.1.102.57467 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [S], seq 1626204761, win 5840, options [mss 1460], length 0
20:27:31.370187 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57467: Flags [S.], seq 3109029923, ack 1626204762, win 65535, options [mss 1440], length 0
20:27:31.439135 IP 192.168.1.102.57467 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 1, win 5840, length 0
20:27:31.534852 IP 192.168.1.102.57467 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [P.], seq 1:350, ack 1, win 5840, length 349: HTTP: GET /weatherstation/updateweatherstation.php?ID=PWS_ID&PASSWORD=PWS_PASS&action=updateraww&realtime=1&rtfreq=5&dateutc=now&baromin=29.91&tempf=80.2&dewptf=42.2&humidity=26&windspeedmph=4.2&windgustmph=4.9&winddir=68&rainin=0.0&dailyrainin=0.0&indoortempf=80.9&indoorhumidity=24 HTTP/1.1
20:27:31.746436 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57467: Flags [.], ack 350, win 65535, length 0
20:27:32.798324 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57467: Flags [P.], seq 1:286, ack 350, win 65535, length 285: HTTP: HTTP/1.1 200 OK
20:27:32.956477 IP 192.168.1.102.57467 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 286, win 5555, length 0
20:27:33.062343 IP 192.168.1.102.57467 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [F.], seq 350, ack 286, win 5555, length 0
20:27:33.273637 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57467: Flags [F.], seq 286, ack 351, win 65535, length 0
20:27:33.383379 IP 192.168.1.102.57467 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 287, win 5554, length 0
20:27:36.379004 ARP, Request who-has 192.168.1.102 tell unifi.localdomain, length 46
20:27:36.454454 ARP, Reply 192.168.1.102 is-at 34:00:8a:e2:3c:46 (oui Unknown), length 46
20:27:43.219832 IP 192.168.1.102.57468 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [S], seq 1626595961, win 5840, options [mss 1460], length 0
20:27:43.429626 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57468: Flags [S.], seq 4078397534, ack 1626595962, win 65535, options [mss 1440], length 0
20:27:43.516847 IP 192.168.1.102.57468 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 1, win 5840, length 0
20:27:43.612591 IP 192.168.1.102.57468 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [P.], seq 1:350, ack 1, win 5840, length 349: HTTP: GET /weatherstation/updateweatherstation.php?ID=PWS_ID&PASSWORD=PWS_PASS&action=updateraww&realtime=1&rtfreq=5&dateutc=now&baromin=29.91&tempf=80.2&dewptf=42.2&humidity=26&windspeedmph=4.2&windgustmph=4.9&winddir=68&rainin=0.0&dailyrainin=0.0&indoortempf=80.9&indoorhumidity=24 HTTP/1.1
20:27:43.823260 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57468: Flags [.], ack 350, win 65535, length 0
20:27:43.843881 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57468: Flags [P.], seq 1:286, ack 350, win 65535, length 285: HTTP: HTTP/1.1 200 OK
20:27:43.958951 IP 192.168.1.102.57468 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 286, win 5555, length 0
20:27:44.114567 IP 192.168.1.102.57468 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [F.], seq 350, ack 286, win 5555, length 0
20:27:44.324568 IP a3.bc.7434.ip4.static.sl-reverse.com.80 > 192.168.1.102.57468: Flags [F.], seq 286, ack 351, win 65535, length 0
20:27:44.327000 IP 192.168.1.102.57468 > a3.bc.7434.ip4.static.sl-reverse.com.80: Flags [.], ack 287, win 5554, length 0
