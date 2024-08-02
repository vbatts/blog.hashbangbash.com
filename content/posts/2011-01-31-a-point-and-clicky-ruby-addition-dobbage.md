---
title: a point-and-clicky (ruby) addition, dobbage!
author: vbatts
layout: post
date: 2011-01-31T05:41:33+00:00
url: /2011/01/a-point-and-clicky-ruby-addition-dobbage/
aktt_notify_twitter:
  - no
  - no
tags:
  - Community
  - Projects
  - slackware
tags:
  - qt
  - ruby
  - slackware

---
Some time ago, the brainstorm of a simple GUI package viewer for my Slackware desktop would be handy. While completely unnecessary, I thought it would be neat.
  
So what did I do? Created a project on github.com for an application that did not exist yet, and commenced to reading the source of others with similarly simple application frameworks.

While having the project created out on github was a reminder, it was not much for encouragement, so the idea lay dormant for a while. With a free moment this past weekend, I solidified several ideas and actually sought to make headway on this task. But I will say, that having the project on github for so long, has increased the rank on [Google&#8217;s search results for &#8220;dobbage&#8221;][1] so that is a plus <img src="/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" />

While I am crafty with many tools/languages, my weapon of choice remains <a target="_blank" href="http://ruby-lang.org">ruby</a>. For my development time, I can get an idea prototyped very quick. At which point I can decided whether it is worth it to use another language (for performance, etc.). All that to say, dobbage is written in Ruby.

Initially, I had thought that <a target="_blank" href="http://ruby-gnome2.sourceforge.jp/">ruby-gtk2</a> would be the windowing toolkit to use, but then [Qt][2] and [QtRuby][3] are included in the stock Slackware installation.
  
I must say, there is not an abundance of applications to find, that are written in QtRuby, the bindings to access the Qt libraries is **very** nice and extensive. This is my first &#8220;GUI&#8221; application, so it&#8217;s all new to me. The <a target="_blank" href="http://doc.qt.nokia.com/latest/classes.html">Qt API documentation</a> is written for C++, but once you get the hang of translating their C++ explanation to a Ruby implementation, then it&#8217;s all gravy! <img src="/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" />

Dobbage only has one dependency, that is not included in a stock Slackware install, and that is my <a target="_blank" href="https://github.com/vbatts/slack-utils">slack-utils ruby library</a>, which can be built as an installable Slackware package, or available as a <a target="_blank" href="https://rubygems.org/gems/slack-utils">rubygem</a>. slack-utils does provide several command line tools, but more importantly, it includes a library for a Slackware Linux harness.

Right now, dobbage only shows information for packages currently installed, and for &#8220;removed&#8221; packages. Removed packages include previous iterations of upgraded packages. There is a little fancy-ness in the status bar too. Other ideas include the ability:

  * sorting the Removed packages by date removed, instead of alphabetical
  * to open an installable Slackware package (a *.t?z archive), and get a tab with information about that package.
  * to have a tab for fetching information from a Slackware repository /mirror
  * good ideas from others <img src="/wp-includes/images/smilies/simple-smile.png" alt=":)" class="wp-smiley" style="height: 1em; max-height: 1em;" />

In any event, the code is available at [github.com/vbatts/dobbage][4] and here are a few [screen shots][5].

Take care,
  
vb

 [1]: http://www.google.com/search?q=dobbage
 [2]: http://en.wikipedia.org/wiki/Qt_(framework)
 [3]: http://en.wikipedia.org/wiki/QtRuby
 [4]: http://github.com/vbatts/dobbage
 [5]: http://connie.slackware.com/~vbatts/dobbage/