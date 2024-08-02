---
title: Getting started with compiling Wii apps in Linux
author: vbatts
layout: post
date: 2010-09-03T13:26:43+00:00
url: /2010/09/getting-started-with-compiling-wii-apps-in-linux/
aktt_notify_twitter:
  - no
  - no
tags:
  - Community
  - ISV
  - Linux

---
First off, setting up a development environment, and then the ability to run the games on the Wii are very different. Since my Wii was on the latest official system upgrade (v4.3), there were not many options for getting a custom game boot loader on it. Thankfully, I went with the [Indiana Pwns][1]{#aptureLink_RVqygFnr81} and it was super easy and straight forward. Now I have the [Homebrew Loader installed by way of using the BootMii installer][2]{#aptureLink_MAlJZ59YAt}, and I am free to load my own compiled projects as well.

To get the development kit up and operational it involved a bit of guess work, but thankful I can still use my GNU C utilities.

From the &#8220;devkitPPC&#8221; directory, you&#8217;ll need the appropriate download for you host type. For me, it was devkitPPC\_r21-x86\_64-linux.tar.bz2

﻿﻿﻿﻿﻿<http://sourceforge.net/projects/devkitpro/files/>

It extracts to ﻿&#8221;devkitPPC/&#8221;. You can put this where every you&#8217;d like, but I kept everything as my limited privilege user, and created a directory $HOME/bin/opt/, and put the directory there. Next you&#8217;ll need to set a couple of ENV variables that most projects look for in their Makefile&#8217;s. In my $HOME/.bashrc I added

<div id="_mcePaste">
  export DEVKITPPC=$HOME/bin/opt/devkitPPC/
</div>

<div id="_mcePaste">
  export DEVKITPRO=$DEVKITPPC
</div>

<div id="_mcePaste">
  export MANPATH=$MANPATH:$DEVKITPPC/man
</div>

<div>
  export PATH=$PATH:$DEVKITPPC/bin
</div>

The manpath is for me <img src="/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" />

From the portlibs folder you&#8217;ll need everything with &#8220;ppc&#8221; in the name. The [Wii&#8217;s proccesor is an IBM PowerPC chipset][3]{#aptureLink_ouZn6OyvJY}

<http://sourceforge.net/projects/devkitpro/files/portlibs>

Once you have all of these downloaded, a simple for loop will do well.

mkdir ${DEVKITPPC}/portlibs

for file in *ppc.tar.bz2 ; do tar -C ${DEVKITPPC}/portlibs/ -xjf $file ; done

Next, download libogc-src-*.tar.bz2 from <http://sourceforge.net/projects/devkitpro/files/libogc/>, and lets get it compiled and installed (also, it depends on the ENV variables and the libraries above). The make install below will put it in $DEVKITPPC.

mkdir libogc ; tar -C libogc -xjf libogrc-src-*.tar.bz2 ; cd libogc

make clean && make && make install

Lastly, getting libfat present and respectable. <http://sourceforge.net/projects/devkitpro/files/libfat/> get the libfat-ogc-*.tar.bz2 tar ball

tar -C ${DEVKITPPC}/portlibs/ -xjf libfat-ogc-*.tar.bz2

cp ${DEVKITPPC}/portlibs/lib/wii/libfat.a ${DEVKITPPC}/portlibs/lib/libfat.a

At this point there is enough present to begin hacking or least compiling as a tester. To validate we have a good compiling ENV, lets try out [libwiigui][4]{#aptureLink_A3ry32aSYn} . It is a great framework to learn/use/get started with, but for now if we use it to a validation test. At the time of writing this, the latest was libwiigui-1.05.zip. So:

mkdir libwiigui ; cd libwiigui ; unzip ../libwiigui-1.05.zip

make clean && make

You should be left with two  new files (libwiigui-demo.elf and libwiigui-demo.dol).

On the SD card that I keep my HomeBrew applications on, I make a directory sd://apps/libwiigui/ and copy these two files into that directory, and rename them boot.elf and boot.dol respectively.

Now when I put the SD card in the Wii and launch the HomeBrew loader, I have a blank button (because I didn&#8217;t take the time to add icons and descriptions), and when clicked and loaded, there it the basic libwiigui framework app!

YAHTZEE!!

Take care,

vb

p.s. here are a couple of additional information links.

<http://wiibrew.org/wiki/Libwiigui/tutorial>

<http://wiibrew.org/wiki/Developer_tips>

 [1]: http://wiibrew.org/wiki/Indiana_Pwns
 [2]: http://bootmii.org/download
 [3]: http://en.wikipedia.org/wiki/Wii
 [4]: http://code.google.com/p/libwiigui/