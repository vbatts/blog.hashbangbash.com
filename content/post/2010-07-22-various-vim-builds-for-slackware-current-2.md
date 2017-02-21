---
title: Various VIM builds for slackware -current
author: vbatts
layout: post
date: 2010-07-23T02:20:22+00:00
url: /2010/07/various-vim-builds-for-slackware-current-2/
aktt_notify_twitter:
  - no
  - no
categories:
  - Linux
  - Projects
  - slackware

---
In the past couple of days I have been staying unusually aware of updates and patches from the <a href="http://www.vim.org/" target="_blank">VIM project</a>. I have <a href="http://cardinal.lizella.net/~vbatts/updates/vim/" target="_blank">uploaded several packages</a> that have the latest patches of the 7.2 branch. Then for kicks and giggles I pulled down the <a href="http://groups.google.com/group/vim_dev/browse_thread/thread/952942e9c515e536?pli=1" target="_blank">7.3a unstable build</a>, but it is nothing released or ready.

One thing in particular, the 7.2.446 build **does** have `--enable-rubyinterp` compiled in, for supporting the <a href="http://www.ruby-lang.org" target="_blank">Ruby interpreter</a>. Someone had sent a link for the <a href="http://www.vim.org/scripts/script.php?script_id=1890" target="_blank">lusty-explorer</a> script for VIM. While the prospects of the script look great, I was unable to give it whirl, since the stock VIM in slackware currently does not have the ruby interpreter compiled in.

Enjoy, and feel free to drop me your feedback.

Take care,

vb