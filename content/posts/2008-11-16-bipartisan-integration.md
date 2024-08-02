---
title: bipartisan integration !!
author: vbatts
layout: post
date: 2008-11-16T19:16:42+00:00
url: /2008/11/bipartisan-integration/
tags:
  - Community

---
so i have been frustrated for some time that OSX 10.5 does not play well with standard linux nfs mounts. this is quite disheartening since that&#8217;s the back bone of the infrastructure.

but today! on a whim i googled for &#8216;sshfs osx&#8217; and bam!

<a href="http://www.pqrs.org/tekezo/macosx/sshfs/" target="_blank">http://www.pqrs.org/tekezo/macosx/sshfs/ </a>

it did require a reboot, but the mounting is done as user, and is as easy as

> mac:~ vbatts$ mkdir ~/storage
  
> mac:~ vbatts$ /Applications/sshfs/bin/mount_sshfs -L vbatts@media: ~/storage 

the &#8216;-L&#8217; follows symlinks, which is helpful for my setup 

cheers!

vb