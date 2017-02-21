---
title: More 1337 for your Slackware-13.37 release
author: vbatts
layout: post
date: 2011-04-11T19:42:17+00:00
url: /2011/04/more-1337-for-your-slackware-13-37-release/
aktt_notify_twitter:
  - no
  - no
categories:
  - Linux
  - slackware
tags:
  - kernel
  - slackware

---
<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
``<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
`` 
  
If you are running the &#8216;-smp&#8217; kernel, then append &#8216;-smp&#8217; to the kernel version. To determine if you are, then run
  
`$> uname -r`

The assumption with this option, is that this new vmlinuz will be using the kernel modules provided with the package
  
&#8216;kernel-modules-2.6.37.6*&#8217;. 



<font size="+2"><b>IF you want</b></font> to differentiate this kernel from the stock installed kernel, open the &#8216;.config&#8217; file, search for the CONFIG_LOCALVERSION line, and set the value to something brilliant, like
  
```<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
``<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
`` 
  
If you are running the &#8216;-smp&#8217; kernel, then append &#8216;-smp&#8217; to the kernel version. To determine if you are, then run
  
`$> uname -r`

The assumption with this option, is that this new vmlinuz will be using the kernel modules provided with the package
  
&#8216;kernel-modules-2.6.37.6*&#8217;. 



<font size="+2"><b>IF you want</b></font> to differentiate this kernel from the stock installed kernel, open the &#8216;.config&#8217; file, search for the CONFIG_LOCALVERSION line, and set the value to something brilliant, like
  
``` 
  
This however, will require to (re)compile all the modules and the bzImage kernel. So then it may be just as easy to do 
  
````<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
``<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
`` 
  
If you are running the &#8216;-smp&#8217; kernel, then append &#8216;-smp&#8217; to the kernel version. To determine if you are, then run
  
`$> uname -r`

The assumption with this option, is that this new vmlinuz will be using the kernel modules provided with the package
  
&#8216;kernel-modules-2.6.37.6*&#8217;. 



<font size="+2"><b>IF you want</b></font> to differentiate this kernel from the stock installed kernel, open the &#8216;.config&#8217; file, search for the CONFIG_LOCALVERSION line, and set the value to something brilliant, like
  
```<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
``<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
`<font size="+2"><b>If you are</b></font> one that is not afraid to recompile your kernel, then here is a little treat for you.
   
[http://slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz][1]

This patch applies to the Linux kernel source stored in /usr/src/linux of your Slackware Linux 13.37 install. If you do not have a &#8216;.config&#8217; file present, then do
  
` 
  
Assuming there are no errors, then you can proceed along with
  
`` 
  
If you are running the &#8216;-smp&#8217; kernel, then append &#8216;-smp&#8217; to the kernel version. To determine if you are, then run
  
`$> uname -r`

The assumption with this option, is that this new vmlinuz will be using the kernel modules provided with the package
  
&#8216;kernel-modules-2.6.37.6*&#8217;. 



<font size="+2"><b>IF you want</b></font> to differentiate this kernel from the stock installed kernel, open the &#8216;.config&#8217; file, search for the CONFIG_LOCALVERSION line, and set the value to something brilliant, like
  
``` 
  
This however, will require to (re)compile all the modules and the bzImage kernel. So then it may be just as easy to do 
  
```` 

Explanation of events here:

  * the vmlinux-* is usually around 18mb, and primarily used when debugging. 
  * the lib/firmware/ would clobber the stock kernel-firmware package. no need in doing that. 
  * the ${ts} is just a timestamp of epoch time. nothing fancy. 
  * makepkg will create a Slackware package with a distinguished name and version, so as to not interfere with the stock packages 
  * change the &#8216;_mine&#8217; in the package name, if you have your own tagging name for packages you&#8217;ve created for your own system. 



<font size="+2"><b>In Either Event</b></font>, you will need to: 

  * create an initrd, with mkinitrd, if you use one
  * adjust your /etc/lilo.conf accordingly
  * re-run \`lilo\`
  * reboot
  * enjoy the l33t penguins



<font size="+2"><b>If You Want</b></font> to fetch the kernel source, rather than use /usr/src/linux, download it here <http://www.kernel.org/pub/linux/kernel/v2.6/linux-2.6.37.6.tar.bz2>
  

  
<font size="+2"><b>Also</b></font>, so that you are not suspect of malicious kernel patches. The patch adds &#8216;drivers/video/logo/logo\_slk\_clut224.ppm&#8217;, enables this logo image to be chosen with the config option of &#8216;CONFIG\_LOGO\_SLK_CLUT224&#8217;. 
  
Then BEHOLD!
  
[<img src="http://blog-mindful.rhcloud.com/wp-content/uploads/2011/04/logo_slk_clut224.gif" alt="" title="logo_slk_clut224" width="80" height="80" class="alignnone size-full wp-image-225" />][2] 



Take care,
  
vb

 [1]: http://connie.slackware.com/~vbatts/things/linux-2.6.37.6-logo_slk.patch.gz
 [2]: http://blog-mindful.rhcloud.com/wp-content/uploads/2011/04/logo_slk_clut224.gif