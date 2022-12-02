---
title: 'huludesktop for linux &#8230; and slackware !!'
author: vbatts
layout: post
date: 2009-10-09T18:08:53+00:00
url: /2009/10/huludesktop-for-linux-and-slackware/
categories:
  - ISV
  - Linux
  - slackware

---
so i just saw that <a href="http://www.hulu.com/" target="_blank">hulu.com</a> labs has recently published a <a href="http://www.hulu.com/labs/hulu-desktop-linux" target="_blank">desktop player for linux</a>. using my <a href="http://github.com/vbatts/SlackBuilds/tree/master/projects/rpm-slackpack/" target="_blank">rpm-slackpack script</a> , i converted the fedora packages they have for download, into slackware tgz&#8217;s.

you can download the [i386][1] and [x86_64][2] from cardinal.lizella.net/~vbatts/

once you&#8217;ve downloaded and installed it, pending you have the <a href="http://slackbuilds.org/repository/13.0/multimedia/flash-player-plugin/" target="_blank">flash-player-plugin</a> installed from slackbuilds.org, or some other means, you will need to edit the file ~/.huludesktop, on the line &#8220;flash_location = (null)&#8221;, to the location of the flash library. For me, its as follows.

<pre>$ grep libflash /var/log/packages/flash-player-plugin-10.0_r32-x86_64-1_SBo
usr/lib64/mozilla/plugins/libflashplayer.so</pre>

then you have a command \`huludesktop\`, and away you go!

Take care,

vb

 [1]: http://cardinal.lizella.net/~vbatts/downloads/huludesktop-0.9.2-i386-1.tgz
 [2]: http://cardinal.lizella.net/~vbatts/downloads/huludesktop-0.9.2-x86_64-1.tgz