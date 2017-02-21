---
title: 'blackberry modem&#8230; update'
author: vbatts
layout: post
date: 2008-05-13T03:57:34+00:00
url: /2008/05/blackberry-modem-update/
categories:
  - Community
  - ISV
  - Linux
  - Projects
  - slackware

---
so to make the process even easier for connecting to the internet, using a blackberry as the GPRS modem, on slackware, i put together the packages needed for wvdial.
  
after initially attempting pppd, and going with wvdial on my <font color="#ffffff"><a href="http://www.opensuse.org">openSuSE</a></font> work laptop, i thought i would tough it out on my personal slackware laptop, and try to work out the needed chat scripts.
  
BAIL,
  
so the SlackBuilds are [wvdial][1] and [wvstreams][2], these are also served on subversion, so you can &#8216;svn co&#8217; their URL.
  
this makes it **much** more easy to connect, at least with [Sprint][3] it does.

 [1]: http://batts.mine.nu/pub/src/SlackBuilds/wvdial/
 [2]: http://batts.mine.nu/pub/src/SlackBuilds/wvstreams/
 [3]: http://www.sprint.com/