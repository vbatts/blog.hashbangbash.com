---
title: Openoffice.org 3.1.0 on Dell Mini 9 with Ubuntu 8.04.1
author: vbatts
layout: post
date: 2009-05-25T20:35:40+00:00
url: /2009/05/openofficeorg-310-on-dell-mini-9-with-ubuntu-8041/
categories:
  - Community
  - ISV
  - Linux
  - Projects

---
So I have \*finally\* received the <a href="http://www.dell.com/content/products/productdetails.aspx/laptop-inspiron-9?c=us&cs=19&l=en&ref=lthp&s=dhs" target="_blank">9 inch Dell mini</a> that I had ordered over a month ago. The device pre-installed with <a href="http://ubuntu.com" target="_blank">Ubuntu</a> was much more preferable, even though the release is a couple of versions old now.

Everything is great so far, but the first thing that i had to do was update the version of OpenOffice.org being used. Its not so much that I have any problems with version 2.4, but rather everything else I am using is version 3.0 or 3.1.  I have seen several issues when opening and transferring an *.odt between varying versions of openoffice.  Its never fun to lose or mess up formating.

Luckily for me, there were only a couple of dependencies that had to be resolved and the packages I lost, didn&#8217;t really impact me.

First off, I had to add the project that maintains the lastest OOo for Ubuntu. Either through the GUI or to /etc/apt/sources.list, add

<pre>deb http://ppa.launchpad.net/openoffice-pkgs/ubuntu hardy main</pre>

and then wget their key and import it. I panzied out and used the GUI to import it.

<pre>http://ppa.launchpad.net/openoffice-pkgs/ubuntu/dists/hardy/Release.gpg</pre>

you next apt-get update, and should remove the following to packages, and all their dependencies.

<pre>aptitude remove openoffice.org-l10n-en-za openoffice.org-l10n-en-gb</pre>

at which point, you are clear to do an

<pre>aptitude dist-upgrade</pre>

all done!

take care,

vb