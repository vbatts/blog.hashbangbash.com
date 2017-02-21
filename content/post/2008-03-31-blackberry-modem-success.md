---
title: 'blackberry modem&#8230; SUCCESS!'
author: vbatts
layout: post
date: 2008-03-31T22:29:16+00:00
url: /2008/03/blackberry-modem-success/
categories:
  - ISV
  - Linux
  - Projects

---
Last night i got to nerding it up again, this time with the 8703e blackberry, work phone, and linux.

Using [this howto][1]. My blackberry service is through Sprint, so my primary difficulty was with get the pppd settings to work, not until i began scrolling through some of the comments that i found someone using wvdial instead. once i tried it, that worked on the first try. i found on the BlackBerry website, they actually recommend that you can call your service provide and ask what APN can be used. from this howto, the APN is the &#8220;wap.voicestream.com&#8221;. either way should achieve the same end result.
  

  
the openmotif and openmotif-devel i was able to find in a typical SuSE-10.2 repository, but the Xlt libraries and the XmBlackBerry i had to download and compile from source, which was pretty straight forward.
  

  
if you are a slacker, i&#8217;ve made the SlackBuild scripts for these three packages, currently they are  [available here][2] (xlt, openmotif, and xmblackberry)

cheers

vb

 [1]: http://wiki.colar.net/tethering_with_blackberry_pearl_on_linux
 [2]: http://batts.mine.nu/pub/src/SlackBuilds/