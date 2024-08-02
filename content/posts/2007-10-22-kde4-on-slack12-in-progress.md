---
title: 'kde4 on slack12 &#8211; in progress'
author: vbatts
layout: post
date: 2007-10-22T17:09:55+00:00
url: /2007/10/kde4-on-slack12-in-progress/
tags:
  - Community
  - Linux
  - Projects

---
first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
        `````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
````` 
        
        success on kdesupport compiling and installing </li> 
        
        ( while this is going on, i&#8217;m removing all of the kde3 packages installed as standard, and i had downloaded the tgz&#8217;s for easy re-install .
     
        primarily because the kde3 install is in /usr/bin and it&#8217;s advisable to not have it in the $PATH of the kde4 install and user. )
   
        ( also, also, to save time i&#8217;m checking out the kdepimlibs from trunk while kdelibs is compiling
     
        kindof multi-tasking on this AMD 900mhz machine, current machine stats
     
        kde-devel@slacker:~$ uptime
     
        14:37:56 up 18 days, 1:06, 4 users, load average: 4.82, 3.93, 2.66
     
        )
        
          * Moving on to the kdelibs
	  
            and it has just errored out 50% of the way through
	  
            after digging through the diferent outputs, it appears to be referring to an enchant package that wasn&#8217;t installed. so i looked for its source package and found it on a debian site, so i download it and its patch. ( [first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
        `````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
````` 
        
        success on kdesupport compiling and installing </li> 
        
        ( while this is going on, i&#8217;m removing all of the kde3 packages installed as standard, and i had downloaded the tgz&#8217;s for easy re-install .
     
        primarily because the kde3 install is in /usr/bin and it&#8217;s advisable to not have it in the $PATH of the kde4 install and user. )
   
        ( also, also, to save time i&#8217;m checking out the kdepimlibs from trunk while kdelibs is compiling
     
        kindof multi-tasking on this AMD 900mhz machine, current machine stats
     
        kde-devel@slacker:~$ uptime
     
        14:37:56 up 18 days, 1:06, 4 users, load average: 4.82, 3.93, 2.66
     
        )
        
          * Moving on to the kdelibs
	  
            and it has just errored out 50% of the way through
	  
            after digging through the diferent outputs, it appears to be referring to an enchant package that wasn&#8217;t installed. so i looked for its source package and found it on a debian site, so i download it and its patch. (][2] )
	  
            i&#8217;ve configure&#8217;d it (it and kde are referring to a missing hspell lib, which is for Hebrew spell checking? not going there) , compiling it, and have intalled it as root
  
            also its was referring to missing JasPer (<http://www.ece.uvic.ca/~mdadams/jasper/software/jasper-1.900.1.zip>)
	  
            i&#8217;ve configure&#8217;d it, compiling it, and installed it as root
  
            also its was reffering to a missing OpenEXR package, so i found its source at <http://www.openexr.com/> i&#8217;ve downloaded <http://download.savannah.nongnu.org/releases/openexr/openexr-1.6.0.tar.gz>, we&#8217;ll give it a try instead. and this refers to a ilmbase (<http://download.savannah.nongnu.org/releases/openexr/ilmbase-1.0.0.tar.gz>).
	  
            so i&#8217;ve compiled and installed the ilmbase, as root.
	  
            since its&#8217; installed needed lib&#8217;s, i had to run ldconfig so that the ./configure of OpenEXR found the needed lib&#8217;s
	  
            and success on that building, now back to kdelibs 
          * kdelibs is failing again, something about a soprano class that is not being properly referenced. after going back to the kdesupport src dir, the soprano module says it is not being compiled, so now to figure why that is.
	  
            kdesupport has a couple of lib&#8217;s that its is complaining are not there, Exiv2 (<http://www.exiv2.org/exiv2-0.15.tar.gz>), CppUnit (<http://www.slacky.eu/repository/slackware-12.0/development/cppunit/1.12.0/cppunit-1.12.0-i486-1as.tgz>) and the Java SDK (<http://slackware.osuosl.org/slackware-12.0/extra/jdk-6/jdk-6u2-i586-2.tgz>). this is just odd, after i had downloaded the cppunit slack pack, and was waiting on the jdk-6u2 slack pack. i attempted to \`cmakekde\` in the kdesupport dir, and it appears something has removed /usr/bin/makeobj from the system. so i&#8217;ve re-established it with the version provided on the kde4 build site. after installing these dependencies, kdesupport and all of it&#8217;s modules build and install.
	  
            so back to kdelibs, and its just not building, so i&#8217;ve tried \`svn up\` to see if there are later commits to fix my issues, and now \`cmakekde\` has made it from 50% to 94% and has died on a khtml process, so i cd&#8217;d back to the kdelibs src directory, and into khtml. called \`make -j2\`, and it built 100%. so i backed into the kdelibs src dir again, and instead of calling the cmakekde function, i&#8217;ve just called \`make -j2\`, and it build 100%, so i&#8217;ve called \`make install\`.
	  
            i guess this is success on kdelibs, it feels kindof bootlegged though. after this i&#8217;ve run ldconfig as root again, to rebuild the lib index 
          * on to kdepimlibs, cd&#8217;d into the src dir, \`cmakekde\` and roughly 20minutes later 100% built and installed 
              *   * on to kdebase, in its configuration setup, its complaining about networkManager and Capseo not being installed, i&#8217;ve heard bad things about networkManager and slackware, so i&#8217;m not going to bother going down that road with this install. and Capseo is for handling capturing video, while that sounds nice. its not needed for this install.
	  
                    since this could take a while, and its past midnight right now, i&#8217;ve called the build as \`time cmakekde &#038;>/dev/null &#038;\`, so i can just let it run and read about it later.
	  
                    it did not build by itself, so i cd&#8217;d into the runtime and \`cmakekde\` it alone, and it built and installed.
	  
                    next i cd&#8217;d into the workspace directory of kdebase, and it imediately gave a cmake error. so i \`svn up\`&#8217;d, which downloaded a lot. so i resumed, and it is building now, but had complained about a few missing lib&#8217;s, libxklavier, libcaptury, NetworkManager, libnm-util, and RAW1394. of this i will just include the libxklavier from http://internap.dl.sourceforge.net/sourceforge/gswitchit/libxklavier-3.3.tar.gz, and now i&#8217;ve download NetworkManager from http://ftp.gnome.org/pub/GNOME/sources/NetworkManager/0.6/NetworkManager-0.6.5.tar.bz2) and you&#8217;ll need the flag &#8216;&#8211;without-gnome&#8217; unless you have the whole core of gnome installed, and it requires dhcdbd, and i haven&#8217;t found a good source bundle for this yet, so i&#8217;m gonna hold on this for now.
	  
                    while i&#8217;m at it i&#8217;ve download a source bundle of libcaptury at ubuntu (<http://archive.ubuntu.com/ubuntu/pool/universe/libc/libcaptury/libcaptury_0.3.0+svn20070725.orig.tar.gz>)., and capseo. </p> 
                    ok, so runtime is compiling fine, but the workspace is constantly hanging up. currently the fatal error is on
  
                    ``````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
        `````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
````` 
        
        success on kdesupport compiling and installing </li> 
        
        ( while this is going on, i&#8217;m removing all of the kde3 packages installed as standard, and i had downloaded the tgz&#8217;s for easy re-install .
     
        primarily because the kde3 install is in /usr/bin and it&#8217;s advisable to not have it in the $PATH of the kde4 install and user. )
   
        ( also, also, to save time i&#8217;m checking out the kdepimlibs from trunk while kdelibs is compiling
     
        kindof multi-tasking on this AMD 900mhz machine, current machine stats
     
        kde-devel@slacker:~$ uptime
     
        14:37:56 up 18 days, 1:06, 4 users, load average: 4.82, 3.93, 2.66
     
        )
        
          * Moving on to the kdelibs
	  
            and it has just errored out 50% of the way through
	  
            after digging through the diferent outputs, it appears to be referring to an enchant package that wasn&#8217;t installed. so i looked for its source package and found it on a debian site, so i download it and its patch. ( [first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
        `````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
    ````first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
    ```first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
    ``first off, <http://techbase.kde.org/Getting_Started/Build/KDE4>

the actual kde4 and qt environment will be within the user &#8216;kde-devel&#8217;, not on the root filesystem.

  * first potential issue, is it prefers g++ 4.2, slack has 4.1.2
  * the \`makeobj\` script was already installed. the install version had added a verbose flag.
  * downloaded, compiled and installed (as root) shared mime info (with no special options/flags)
	  
    `wget <a href="http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2">http://freedesktop.org/~hadess/shared-mime-info-0.22.tar.bz2</a>`
  * boost, I downloaded ([http://internap.dl.sourceforge.net/sourceforge/boost/boost\_1\_34_1.tar.bz2][1]), extracted it to &#8216;/usr/local/boost\_1\_34_1&#8217; and created a sym link to it as &#8216;/usr/local/include/boost&#8217; 
  * cmake appears to already be installed cmake version 2.4-patch 6 
  * downloading the source to redland (<http://download.librdf.org/source/redland-1.0.6.tar.gz>) for &#8216;librdf &#8216;, but i&#8217;ve also found a slack pack at (<http://www.slacky.eu/repository/slackware-12.0/libraries/redland/1.0.6/redland-1.0.6-i486-1as.tgz>).
  
    i&#8217;ll try source first, which appears to have done fine, built in /home/vbatts/src/redland-1.0.6, installed as root 
  * clucene, checked it out from their svn. <http://clucene.sourceforge.net/index.php/Downloads>
	  
    compiled it, and installed it to it&#8217;s default &#8216;/usr/local/&#8217; 
  * /var/log/packages/dbus-1.0.2-i486-3 is already installed 
  * /var/log/packages/dbus-glib-0.73-i486-1 is already installed 
  * /var/log/packages/cmake-2.4.6-i486-2 is already installed 
  * environment variables are almost identical the the sample bashrc provided on the KDE site. I did make a number of symlinks though, because login as &#8216;kde-devel&#8217; didn&#8217;t seem to pick up ~/.bashrc, so I &#8216;ln -s&#8217; as ~/.bash_profile and ~/.shrc 
  * Qt
  
`` 
  * so qt-copy is crashing out in Xlib.h for a \_X\_SENTINEL function, having not been initialized. on slackware this will be slightly interesting finding what package is going to solve this unmatched dependency. following is a section of Xlib.h that is crashing and following that is the compiled time error.
  
``` </p> 
    the error
  
```` 
    
    so after a little bit of googling i found and incerted a definition for this \_X\_SENTINEL function in the /usr/include/X11/Xlib.h header. I&#8217;ve attached the diff i created if you want to reference it or re-use it.
    
    success on QT, it is installed. moving on </li> 
    
      * on to the &#8216;kde-support&#8217; stuff, Strigi is requiring of a libz, libbz2, openssl (libcrypto, and libssl)
	  
        /var/log/packages/openssl-0.9.8e-i486-3 is already installed
	  
        /var/log/packages/openssl-solibs-0.9.8e-i486-3 is already installed
    
        and i&#8217;m not finding much info on this libz/libz2 library
    
        the \`cmakekde\` call inside the kdesupport dir, has just errored out 11% precent of the way through it.</p> 
        update, i&#8217;ve found a download at <http://freeware.sgi.com/source/libz/zlib-1.1.4.tar.gz>, i&#8217;m going to install it.
    
        and the libbz2 appears to be a part of the bzip2 package, and this appears to be installed
  
````` 
        
        success on kdesupport compiling and installing </li> 
        
        ( while this is going on, i&#8217;m removing all of the kde3 packages installed as standard, and i had downloaded the tgz&#8217;s for easy re-install .
     
        primarily because the kde3 install is in /usr/bin and it&#8217;s advisable to not have it in the $PATH of the kde4 install and user. )
   
        ( also, also, to save time i&#8217;m checking out the kdepimlibs from trunk while kdelibs is compiling
     
        kindof multi-tasking on this AMD 900mhz machine, current machine stats
     
        kde-devel@slacker:~$ uptime
     
        14:37:56 up 18 days, 1:06, 4 users, load average: 4.82, 3.93, 2.66
     
        )
        
          * Moving on to the kdelibs
	  
            and it has just errored out 50% of the way through
	  
            after digging through the diferent outputs, it appears to be referring to an enchant package that wasn&#8217;t installed. so i looked for its source package and found it on a debian site, so i download it and its patch. (][2] )
	  
            i&#8217;ve configure&#8217;d it (it and kde are referring to a missing hspell lib, which is for Hebrew spell checking? not going there) , compiling it, and have intalled it as root
  
            also its was referring to missing JasPer (<http://www.ece.uvic.ca/~mdadams/jasper/software/jasper-1.900.1.zip>)
	  
            i&#8217;ve configure&#8217;d it, compiling it, and installed it as root
  
            also its was reffering to a missing OpenEXR package, so i found its source at <http://www.openexr.com/> i&#8217;ve downloaded <http://download.savannah.nongnu.org/releases/openexr/openexr-1.6.0.tar.gz>, we&#8217;ll give it a try instead. and this refers to a ilmbase (<http://download.savannah.nongnu.org/releases/openexr/ilmbase-1.0.0.tar.gz>).
	  
            so i&#8217;ve compiled and installed the ilmbase, as root.
	  
            since its&#8217; installed needed lib&#8217;s, i had to run ldconfig so that the ./configure of OpenEXR found the needed lib&#8217;s
	  
            and success on that building, now back to kdelibs 
          * kdelibs is failing again, something about a soprano class that is not being properly referenced. after going back to the kdesupport src dir, the soprano module says it is not being compiled, so now to figure why that is.
	  
            kdesupport has a couple of lib&#8217;s that its is complaining are not there, Exiv2 (<http://www.exiv2.org/exiv2-0.15.tar.gz>), CppUnit (<http://www.slacky.eu/repository/slackware-12.0/development/cppunit/1.12.0/cppunit-1.12.0-i486-1as.tgz>) and the Java SDK (<http://slackware.osuosl.org/slackware-12.0/extra/jdk-6/jdk-6u2-i586-2.tgz>). this is just odd, after i had downloaded the cppunit slack pack, and was waiting on the jdk-6u2 slack pack. i attempted to \`cmakekde\` in the kdesupport dir, and it appears something has removed /usr/bin/makeobj from the system. so i&#8217;ve re-established it with the version provided on the kde4 build site. after installing these dependencies, kdesupport and all of it&#8217;s modules build and install.
	  
            so back to kdelibs, and its just not building, so i&#8217;ve tried \`svn up\` to see if there are later commits to fix my issues, and now \`cmakekde\` has made it from 50% to 94% and has died on a khtml process, so i cd&#8217;d back to the kdelibs src directory, and into khtml. called \`make -j2\`, and it built 100%. so i backed into the kdelibs src dir again, and instead of calling the cmakekde function, i&#8217;ve just called \`make -j2\`, and it build 100%, so i&#8217;ve called \`make install\`.
	  
            i guess this is success on kdelibs, it feels kindof bootlegged though. after this i&#8217;ve run ldconfig as root again, to rebuild the lib index 
          * on to kdepimlibs, cd&#8217;d into the src dir, \`cmakekde\` and roughly 20minutes later 100% built and installed 
              *   * on to kdebase, in its configuration setup, its complaining about networkManager and Capseo not being installed, i&#8217;ve heard bad things about networkManager and slackware, so i&#8217;m not going to bother going down that road with this install. and Capseo is for handling capturing video, while that sounds nice. its not needed for this install.
	  
                    since this could take a while, and its past midnight right now, i&#8217;ve called the build as \`time cmakekde &#038;>/dev/null &#038;\`, so i can just let it run and read about it later.
	  
                    it did not build by itself, so i cd&#8217;d into the runtime and \`cmakekde\` it alone, and it built and installed.
	  
                    next i cd&#8217;d into the workspace directory of kdebase, and it imediately gave a cmake error. so i \`svn up\`&#8217;d, which downloaded a lot. so i resumed, and it is building now, but had complained about a few missing lib&#8217;s, libxklavier, libcaptury, NetworkManager, libnm-util, and RAW1394. of this i will just include the libxklavier from http://internap.dl.sourceforge.net/sourceforge/gswitchit/libxklavier-3.3.tar.gz, and now i&#8217;ve download NetworkManager from http://ftp.gnome.org/pub/GNOME/sources/NetworkManager/0.6/NetworkManager-0.6.5.tar.bz2) and you&#8217;ll need the flag &#8216;&#8211;without-gnome&#8217; unless you have the whole core of gnome installed, and it requires dhcdbd, and i haven&#8217;t found a good source bundle for this yet, so i&#8217;m gonna hold on this for now.
	  
                    while i&#8217;m at it i&#8217;ve download a source bundle of libcaptury at ubuntu (<http://archive.ubuntu.com/ubuntu/pool/universe/libc/libcaptury/libcaptury_0.3.0+svn20070725.orig.tar.gz>)., and capseo. </p> 
                    ok, so runtime is compiling fine, but the workspace is constantly hanging up. currently the fatal error is on
  
`````` 
	  
                    i&#8217;m not sure where this class needs to get defined before being referenced here, so appearantly something isn&#8217;t compiled that should be, which could mean more package chasing.
	  
                    Okay so i&#8217;m back at it, and i&#8217;m frustrated. I&#8217;m about get ugly, and non-standard, but i really want to see a running desktop, before KDE4 is released. </li> 
                    
                      * koffice is referring to a QCA2 package, (<http://delta.affinix.com/download/qca/2.0/qca-2.0.0.tar.bz2>).

 [1]: http://internap.dl.sourceforge.net/sourceforge/boost/boost_1_34_1.tar.bz2
 [2]: http://ftp.debian.org/debian/pool/main/e/enchant/enchant_1.3.0.orig.tar.gz