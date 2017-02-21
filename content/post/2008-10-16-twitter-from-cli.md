---
title: Twitter, from CLI !!
author: vbatts
layout: post
date: 2008-10-17T00:29:09+00:00
url: /2008/10/twitter-from-cli/
categories:
  - Linux
  - Projects
  - slackware

---
so i have been using the [vim][1] plugin, to send [updates from a vim][2] session, be it from command line or from gvim.
  
but it doesn&#8217;t have the capability to view friends timeline or replies. SO, tonight i got to playing with it, and published on [[SVN]][3] and [[HTTP]][4]. you&#8217;ll need to update the USER_PASS variable, and possibly the *SOURCE variable for the Updates command. you can get the RSS URL for you friend timeline from the bottom of your [twitter home page][5].
  
further, this script uses [lynx][6] and [xmlstarlet][7]. ( lynx is standard in slackware and xmlstarlet is available on [slackbuilds.org][8])

enjoy!

 [1]: http://www.vim.org/
 [2]: http://www.vim.org/scripts/script.php?script_id=2124
 [3]: svn://hashbangbash.com/pub/src/scripts
 [4]: http://hashbangbash.com/pub/src/scripts
 [5]: http://twitter.com/home
 [6]: http://www.delorie.com/web/lynxview.html
 [7]: http://xmlstar.sourceforge.net/
 [8]: http://slackbuilds.org/