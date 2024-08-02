---
title: using the new rpm2tgz, with flags!!
author: vbatts
layout: post
date: 2010-03-18T22:41:25+00:00
url: /2010/03/using-the-new-rpm2tgz-with-flags/
aktt_notify_twitter:
  - yes
  - yes
aktt_tweeted:
  - 1
  - 1
tags:
  - Community
  - Linux
  - Projects
  - slackware

---
In <a href="http://carroll.cac.psu.edu/pub/linux/distributions/slackware/slackware64-current/" target="_blank">slackware-current</a> as of 2010-02-23,  the traditional command used for very basic archive conversion of RPMs into a TAR&#8217;d.Gzip&#8217;d archive, just got a hint of flare. <img src="/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" />

Normal usage is typically for easily converting an RPM that you&#8217;d like to review, like a &#8216;src&#8217; RPM.

But you may find yourself saying, if I could just quickly/easily install <xyz>.rpm on my system as slackware package, it would make life a bit easier. Well now there are several flags available, **to be used AT YOUR OWN RISK** 😉

If you execute rpm2tgz with no arguments, it will output the following

<pre>$ rpm2tgz 
/usr/bin/rpm2tgz:  Converts RPM format to standard GNU tar + GNU zip format.
            (view converted packages with "less", install and remove
            with "installpkg", "removepkg", "pkgtool", or manually
            with "tar")

Usage:      /usr/bin/rpm2tgz [OPTION] &lt;file.rpm>
            (Outputs "file.tgz")

  -s    extract the install scripts to /usr/doc/$PRGNAM-$VERSION/
          for review.
  -S    extracts the install scripts to be executed on package installation
          (only pre-install and post-install scripts used)
          USE WITH CAUTION! 
  -n    name the output package using the rpm's metadata
  -r    extract what the rpm's "requires" (dependencies)
          as documention to /usr/doc/$PRGNAM-$VERSION/
  -d    attempt a wellformed slack-desc from the rpm meta data

</pre>

Everything is pretty straight forward on what it does, but lets have an example. Say you want <a href="http://www.hulu.com/labs/hulu-desktop-linux" target="_blank">hulu-desktop</a>, so you download a file called &#8220;huludesktop-x86\_64.rpm&#8221;, and with traditional usage of rpm2tgz, you would have a resulting archive named &#8220;huludesktop-x86\_64.tgz&#8221;. This is fine if you are not going to install it, but if you do plan on installing this archive, now it does not conform to the naming convention.

With the flags available, you can specify the following command

<pre>$ sudo rpm2tgz -srdn huludesktop-x86_64.rpm</pre>

and get a resulting package called &#8220;huludesktop-0.9.7-x86_64-1.tgz&#8221;.

All flags are not needed, for every situation, and particular ones should be used with care (-S), which can and may frequently wreak havoc on your system.

Take care,

vb