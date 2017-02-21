---
title: 'kde4 on slack12 &#8211; in progress (take 2)'
author: vbatts
layout: post
date: 2007-10-27T14:11:01+00:00
url: /2007/10/kde4-on-slack12-in-progress-take-2/
categories:
  - Community
  - Linux
  - Projects

---
  * Okay, again i&#8217;m back at it, but after googling a little, the &#8216;XSyncQueryExtension&#8217; is a function that should be provided in libXext, while the X11/extensions/sync.h performs an extern status XSyncQueryExtension, it hadn&#8217;t been defined yet. so i&#8217;ve download the source from [http://ftp.de.debian.org/debian/pool/main/libx/libxext/libxext_1.0.3.orig.tar.gz][1], and compiling and installing it as root. &#8230; BUT ARGG /var/log/packages/libXext-1.0.3-i486-1 is already installed!! ok, so i&#8217;m not going to install this source bundle. crap, why isn&#8217;t this linking properly. even in the command that fails it has &#8216;-lXext&#8217; flag, but is still an undefined reference,eerrrghh

 [1]: http://ftp.de.debian.org/debian/pool/main/libx/libxext/libxext_1.0.3.orig%3Cbr%3E%3C/a%3E.tar.gz