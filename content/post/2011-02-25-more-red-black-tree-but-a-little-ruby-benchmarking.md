---
title: More Red-Black Tree, but a little Ruby Benchmarking
author: vbatts
layout: post
date: 2011-02-25T20:56:50+00:00
url: /2011/02/more-red-black-tree-but-a-little-ruby-benchmarking/
aktt_notify_twitter:
  - no
  - no
categories:
  - Projects
tags:
  - performance
  - ruby

---
[<img src="http://blog-mindful.rhcloud.com/wp-content/uploads/2011/02/rbtree-graph1-300x186.png" alt="" title="rbtree-graph1" width="300" height="186" class="aligncenter size-medium wp-image-186" />][1]

After hearing a bit about the improvements in the [Rubinius][2] ruby interpreter, I decided to test a handful of the interpreters against the Red-Black tree insertion script I last posted about.
  

  
As a forward, this is completely out of idle curiosity, and with no deep intentions. The results had distinct winners and losers, and for more rich benchmark, this would need to include multiple iterations and some other aspects as well. All that to say, this is not an all encompassing benchmark.
  
The results are the length (in seconds) of time for the execution.
  

  
So using the rbtree.rb, that I [last blogged about][3], using [rvm][4], I have just run it through the following ruby interpreters:

  * ruby 1.9.1p430 (2010-08-16 revision 28998) [x86_64-linux]
  * rubinius 1.3.0dev (1.8.7 5bd938b5 xxxx-xx-xx JI) [x86_64-unknown-linux-gnu]
  * rubinius 1.2.0 (1.8.7 release 2010-12-21 JI) [x86_64-unknown-linux-gnu]
  * jruby 1.5.3 (ruby 1.8.7 patchlevel 249) (2010-09-28 7ca06d7) (Java HotSpot(TM) 64-Bit Server VM 1.6.0_23) [amd64-java]
  * ruby 1.8.7 (2010-12-23 patchlevel 330) [x86_64-linux], MBARI 0x6770, Ruby Enterprise Edition 2011.01
  * ruby 1.9.3dev (2011-02-14 trunk 30873) [x86_64-linux]

From an early look at it, the Rubinius interpreter is on to something good. For this example at least, their current release branch (1.2) is smoking, compared to anything else.
  
To look at the values, that make up the graph above, here is [the spreadsheet][5]
  

  
Take care,
  
vb

 [1]: http://blog-mindful.rhcloud.com/wp-content/uploads/2011/02/rbtree-graph1.png
 [2]: http://rubini.us/
 [3]: http://blog.hashbangbash.com/2011/02/kick-off-of-playing-with-trees/
 [4]: http://rvm.beginrescueend.com/
 [5]: http://blog-mindful.rhcloud.com/wp-content/uploads/2011/02/ruby-benchmark.ods