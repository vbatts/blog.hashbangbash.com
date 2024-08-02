---
title: latest ruby packages for slackware current
author: vbatts
layout: post
date: 2010-07-23T13:40:01+00:00
url: /2010/07/latest-ruby-packages-for-slackware-current/
aktt_notify_twitter:
  - yes
  - yes
aktt_tweeted:
  - 1
  - 1
tags:
  - Projects
  - slackware
tags:
  - ruby
  - slackware

---
Keeping on the track of various projects that have new releases out right now, Ruby has just released a couple of patch releases like ﻿<a href="http://www.ruby-lang.org/en/news/2010/07/02/ruby-1-9-1-p429-is-released/" target="_blank">1.9.1-p429</a>. Which has a list of bugfixes, and only a vulnerability that affects Windows users. Another release just a few days ago, is the <a href="http://www.ruby-lang.org/en/news/2010/07/11/ruby-1-9-2-rc2-is-released/" target="_blank">1.9.2 RC2 release</a>. Which should be interesting for a couple of reasons. They are stating that it should be **mostly** compatible with previous versions.  This is the current problem with getting many ruby projects updated from 1.8.x to 1.9.1.  Now there may be potentially more updates and modifications needed.

Also, unlike the 1.8.x branch that stayed in `/usr/lib/ruby/1.8/`, once they got underway with 1.9.1 the path standard became `/usr/lib/ruby/1.9.1/`, so it&#8217;ll be changing with every sub-major release (so you would think). This is fine and good, and will join the ranks of the layout that other interpreters have taken. I just hope that the community cleanly grabs on to this, otherwise it&#8217;ll make for ugly clean-up. Another thing, is that despite these path changes due to version, there is no option (like there is when compiling perl) to ﻿`-Dinc_version_list=''`. So you can quickly and easy lose libraries that you had built for a previous version. We will have to continue pulling through this 1.9.2RC2 release, because initially there is not a lot of consistency in paths. There are some libraries built out as 1.9.1, and other paths as 1.9.2. Even the default headers have a mix of whether to be in a 1.9.1 or  1.9.2. Hmmm. I have taken steps to avoid these conflicts, but /usr/lib64/libruby.so.1.9.1 will still conflict.

The packages have <a href="http://cardinal.lizella.net/~vbatts/updates/ruby/" target="_blank">landed on cardinal</a> and are to be used at your own risk. I built the 1.9.2 version with a suffix of 192, in efforts to test having multiple versions coexist, but due to the spattering of 1.9.1 conflicts, you should `` `removepkg ruby` `` before installing the 1.9.2 package.

In other neat things about ruby-1.9.2, the Time class has been reimplemented so that the [year 2038][1]{#aptureLink_E2iBKW1K48} is no longer a problem.

Available on <a href="http://cardinal.lizella.net/~vbatts/updates/ruby/" target="_blank">http://cardinal.lizella.net/~vbatts/</a> you&#8217;ll find a ruby18 package. This package does coexist nicely with either ruby or ruby192. I had bundled this up while working with some [rails][2]{#aptureLink_THuZexZZ0D} projects (like [radiant CMS][3]{#aptureLink_TNhoi8kgHo}!) that are not yet ready for ruby-1.9.x

Take care,

vb

 [1]: http://en.wikipedia.org/wiki/Year%202038%20problem
 [2]: http://en.wikipedia.org/wiki/Ruby%20on%20Rails
 [3]: http://radiantcms.org/