---
title: averatec 3250, slackware-12.0 to slackware-current
author: vbatts
layout: post
date: 2008-02-04T01:27:26+00:00
url: /2008/02/averatec-3250-slackware-120-to-slackware-current/
tags:
  - Community
  - Linux

---
slightly easy and brutal at the same time. the steps to actually perform the upgrade were simple, and only like 5 steps.
  
major changes were a moving from kernel 2.6.21.5 -> 2.6.23.12 and to Xorg 7.3.
  
this is where the laptop appearantly had run ins. the [Averatec][1] [3250][2] is a VIA frameworked system down to the Video card, and it has a [Ralink rt2500][3] wireless card.
  
the rt2500 is compiled seperately against the 2.6.23.12 kernel, i did so after i was booted into the the new systems configuration. if i am running on init 3 with no X, i can be on the internet just fine. OR if i startx and do not bring up the ra0 interface, everything works just fine. BUT if have both at the same time, the entire system fatally hangs after about 3 seconds after loads to where you see a mouse cursor, total system, not pingable, nothing.
  
after trying many variations of xorg.conf, or recompiling the [rt2500 drivers][4], nothing seemed to work. but i did find that the &#8216;vesa&#8217; video driver no longer works with this system, on Xorg 7.3.
  
eventually after trying to startx, and kill it as soon as i saw the mouse cursor, to catch it before it hangs, i found that syslogd dumped a message to stdout about the kernel disabling IRQ #11.
  
so after looking through the syslog, it threw something about how nobody cares but the kernel is disabling IRQ #11, try rebooting with irqpoll flags.

i rebooted, adding irqpoll to the lilo prompt, and now i can be in X and bring up ra0. so silly.

then i added &#8216;append = &#8220;irqpoll&#8221; &#8216; to the slack section of /etc/lilo.conf, and ran lilo.

thanks rworkman for brainstorming with me.

 [1]: http://www.averatec.com/
 [2]: http://www.notebookreview.com/default.asp?newsID=2145&#038;review=Averatec+3200
 [3]: http://rt2x00.serialmonkey.com/
 [4]: http://slackbuilds.org/repository/12.0/network/rt2500/