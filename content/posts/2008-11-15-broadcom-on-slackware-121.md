---
title: broadcom on slackware-12.1
author: vbatts
layout: post
date: 2008-11-15T16:52:19+00:00
url: /2008/11/broadcom-on-slackware-121/
categories:
  - Linux
  - slackware

---
so i&#8217;ve heard from numerous people complain about the difficulty of broadcom wireless cards on linux, and i agree, it was not anything straigtforward.
  

  
After finding [b43 info on linuxwireless.org][1] i took there pasted info on
  


> 0c:00.0 Network controller: Broadcom Corporation BCM4312 802.11a/b/g (rev 01) </p>
and pasted it to a **simple** shell script

> wget http://bu3sch.de/b43/fwcutter/b43-fwcutter-011.tar.bz2
  
> tar xjf b43-fwcutter-011.tar.bz2
  
> cd b43-fwcutter-011
  
> make
  
> cd ..
  
> export FIRMWARE\_INSTALL\_DIR=&#8221;/lib/firmware&#8221;
  
> wget http://downloads.openwrt.org/sources/broadcom-wl-4.80.53.0.tar.bz2
  
> tar xjf broadcom-wl-4.80.53.0.tar.bz2
  
> cd broadcom-wl-4.80.53.0/kmod
  
> sudo ../../b43-fwcutter-011/b43-fwcutter -w &#8220;$FIRMWARE\_INSTALL\_DIR&#8221; wl_apsta.o 


  
while that was going on, i built the latest[wicd][2] in the [SlackBuilds.org][3] 12.1 repo, you can go [here][4]
  

  
after adding /etc/rc.d/rc.wicd to rc.local to start on boot, i rebooted, and _magically_ networks are discoverd and availble to connect to !
  
i&#8217;m only on a WEP network right now, so maybe i&#8217;ll have some issues when i need wpa, but for now, we&#8217;re gravy.

vb

 [1]: http://linuxwireless.org/en/users/Drivers/b43 "http://linuxwireless.org/en/users/Drivers/b43"
 [2]: http://wicd.sourceforge.net/ "http://wicd.sourceforge.net/"
 [3]: http://slackbuilds.org
 [4]: http://slackbuilds.org/repository/12.1/network/wicd/ "http://slackbuilds.org/repository/12.1/network/wicd/"