mod_proctitle.c - Apache proctitle module
=================================================

This module sets apache process titles to reflect the request currently processed, and some statistics so they will be visible 
in top(1) or ps(1). Useful for debugging purposes. Very quickly and clearly.

Features
--------

* Listen Queue Lenght (comlete and incomplete). Only for FreeBSD.
* Queries per Second and bitrate statistics for the last minute.
* Remote IP-address, Host and Query String for each handler. Only for prefork MPM.
* Independence of statistics on available handlers.

Description
-----------

Now normally works on FreeBSD and Apache 2.4. Assembly was tested on Apache 2.2.

For handlers displays the IP-address, the host and the query string. The main process collects query per second and bitrate statistics.
In both cases, the parameter shows the listen queue lenght, which is important for problems investigation. Very quickly and clearly.

* [M - master process.
* [I - idle child process. Be careful, the value of his stats in the past.
* [B - "busy" (in work) child process. Hot stats :)
* [C - idle child process without queries. Or non prefork child.
* lq: - "listen queue", complete|incomplete.
* qps: - Queries per second for the last minute.
* rate: - rate in kilobites per second for the last minute.

I used the FreeBSD specific setproctitle() function to specify the title of the process.
I used getsockopt() function with FreeBSD specific SO_LISTENQLEN and SO_LISTENINCQLEN parameters to obtain the values of the listen queue length.

Configuration
-------------

To play with this sample module first compile it into a DSO file and install it into Apache's modules directory by running:

 $ apxs -c -i mod_proctitle.c

To enable mod_proctitle add this lines to working copy of httpd.conf:

 LoadModule proctitle_module modules/mod_proctitle.so
 <IFModule proctitle_module>
     # Default: Off
     ProctitleEnable On
     # Default: ''
     ProctitleIdent pool01
 </IFmodule>

ProctitleIdent can to set from PROCTITLEIDENT enviroment variable

Examples
--------

# ps -aux | grep httpd
root     78931  0.0  0.5 182576 17180  ??  Ss    5:33PM   0:02.04 httpd: [M   lq: 0|0,  qps: 12,  rate: 2371 Kbps]   pool1 (httpd)
charlie       47390  0.0  0.7 192384 22288  ??  I    12:00AM   0:00.30 httpd: [I lq: 0|0] [188.242.150.18] example.com GET /admin/users/ HTTP/1.0 (httpd)
charlie       47391  0.0  0.7 192384 22252  ??  I    12:00AM   0:00.16 httpd: [I lq: 0|0] [217.70.31.114] example.net GET / HTTP/1.1 (httpd)
charlie       47392  0.0  0.7 192384 22368  ??  I    12:00AM   0:00.38 httpd: [I lq: 0|0] [188.242.150.18] example.com GET /admin/edit/92 HTTP/1.0 (httpd)


Donate
------

For Nuts, please.
PayPal: schors@gmail.com
Yandex.Money: 41001140237324

TODO
----

1. Linux implementation of setproctitle(). The problem is poor documentation about Linux and Google littered old and "curves" solutions.
The Important point - to substitute the progname. FreeBSD set it by default. Most rc.d system based on true progname in title.

2. Linux implementation to obtain the values of the listen queue length. I need to study netlink(7) and the basics of working with netlink.
