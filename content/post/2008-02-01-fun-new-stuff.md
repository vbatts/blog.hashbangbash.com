---
title: Fun new stuff
author: vbatts
layout: post
date: 2008-02-01T23:06:11+00:00
url: /2008/02/fun-new-stuff/
categories:
  - Community
  - Life

---
significantly helping to post entries here more often, or easier, i&#8217;ve stumbled upon an app called [blokkal][1], and is super. while it is entirely simple, it does everything for posting, reviewing, retreiving, modifying and/or deleting entries, that you would need for a blogging client to do. the only snag that i had, was solved relatively quickly by the designer [Martin Muller][2]. that is, if you use wordpress, you may need to check a box about a date time work around.
  
i overlooked this, and all of the posts were thirty years in the past, while interesting, it was frustrating me.


  
**also**, because work has been re-arranging, it has required an increasing level of windows usage. so i have been working with [VirtualBox][3], its virtualization software with an open source edition. this has proven to be quite good for virtualzing, even though its not as stabile or point and click as VMWare is.
  
it running a WindowsXP guest on top of an openSuSE 10.3 host.
  
the two most difficult tasks with it have been, to configure a bridged interface, which is not terrible, just remember that suse has its&#8217; default firewall configurations that interfere greatly. secondly, the physical host has an dual Intel Xeon processor, and the kernel is the bigsmp. Appearantly the are a few known issues with VirtualBox surrounding the tickless kernel and potentially smp functionality. I&#8217;ve added `nohz=off` to the boot line parameters and its seemed to alleviate the virtual guest from freezing on a regular basis, but we&#8217;re still watching the machine to test it more, before we use this guest as a _&#8220;Production&#8221;_ box.
  
**also**, in that same thread, we are doing more with their Active Directory, for email and various services. so i have been able to experiment with various linux services that use or work against an LDAP/AD system, with significant progress. Much of which i will try to post, once i&#8217;ve ironned it out and sanitized it 😉


  
_anyhow_
  
later

 [1]: http://blokkal.sourceforge.net/
 [2]: http://orvio.de/
 [3]: http://www.virtualbox.org/