---
title: linux 2.6.32.3 and the Broadcom STA wireless driver
author: vbatts
layout: post
date: 2010-01-18T20:20:27+00:00
url: /2010/01/linux-2-6-32-3-and-the-broadcom-sta-wireless-driver/
aktt_notify_twitter:
  - yes
  - yes
aktt_tweeted:
  - 1
  - 1
tags:
  - Community
  - ISV
  - Linux
  - slackware

---
on the recent release of the 2.6.32 series kernel in the <a href="http://www.slackware.com" target="_blank">slackware-current</a> branch, currently 2.6.32.3, it has posed a bit of a minor frustration on this Dell mini 9&#8243; and its broadcom 4312

    vbatts@mini:~$ lspci | grep Network
    03:00.0 Network controller: Broadcom Corporation BCM4312 802.11b/g (rev 01)

appearantly in the kernel headers, the definition of schedule() has moved around a little. Using the drivers downloaded from <a href="http://www.broadcom.com/support/802.11/linux_sta.php" target="_blank">here</a> (currently version 5.10.91.9.3), if you are getting an error like

<pre>vbatts@current:~/bcm1$ make
KBUILD_NOPEDANTIC=1 make -C /lib/modules/`uname -r`/build M=`pwd`
make[1]: Entering directory `/usr/src/linux-2.6.32.3'
  LD      /home/vbatts/bcm1/built-in.o
  CC [M]  /home/vbatts/bcm1/src/wl/sys/wl_linux.o
/home/vbatts/bcm1/src/wl/sys/wl_linux.c: In function 'wl_free':
/home/vbatts/bcm1/src/wl/sys/wl_linux.c:705: error: implicit declaration of function 'schedule'
make[2]: *** [/home/vbatts/bcm1/src/wl/sys/wl_linux.o] Error 1
make[1]: *** [_module_/home/vbatts/bcm1] Error 2
make[1]: Leaving directory `/usr/src/linux-2.6.32.3'
make: *** [all] Error 2
</pre>

then the following <a href="http://hashbangbash.com/~vbatts/hybrid_sta-2.3.32.patch" target="_blank">patch</a> will just add include the needed headers to move you along in compiling your wl.ko module

    
    cat ../hybrid_sta-2.3.32.patch | patch -p1
    

at which point you should be gravy to proceed as usual.

<pre>vbatts@current:~/bcm2$ make
KBUILD_NOPEDANTIC=1 make -C /lib/modules/`uname -r`/build M=`pwd`
make[1]: Entering directory `/usr/src/linux-2.6.32.3'
LD      /home/vbatts/bcm2/built-in.o
CC [M]  /home/vbatts/bcm2/src/wl/sys/wl_linux.o
CC [M]  /home/vbatts/bcm2/src/wl/sys/wl_iw.o
CC [M]  /home/vbatts/bcm2/src/shared/linux_osl.o
LD [M]  /home/vbatts/bcm2/wl.o
Building modules, stage 2.
MODPOST 1 modules
WARNING: modpost: missing MODULE_LICENSE() in /home/vbatts/bcm2/wl.o
see include/linux/module.h for more information
CC      /home/vbatts/bcm2/wl.mod.o
LD [M]  /home/vbatts/bcm2/wl.ko
make[1]: Leaving directory `/usr/src/linux-2.6.32.3'
vbatts@current:~/bcm2$ ls
Makefile  Module.symvers  built-in.o  lib/  modules.order  src/  wl.ko  wl.mod.c  wl.mod.o  wl.o
vbatts@current:~/bcm2$ sudo mv wl.ko /lib/modules/$(uname -r)/kernel/drivers/net/wireless/
vbatts@current:~/bcm2$ sudo depmod -a
vbatts@current:~/bcm2$ sudo modprobe wl</pre>

and do not forget to \`unset ARCH\` before compiling 😉

Take care,

vb