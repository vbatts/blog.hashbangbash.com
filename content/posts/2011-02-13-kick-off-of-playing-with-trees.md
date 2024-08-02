---
title: Kick off of playing with trees
author: vbatts
layout: post
date: 2011-02-14T04:07:12+00:00
url: /2011/02/kick-off-of-playing-with-trees/
aktt_notify_twitter:
  - yes
  - yes
aktt_tweeted:
  - 1
  - 1
tags:
  - Projects
  - thoughts
tags:
  - ruby
  - thoughts

---
As I have stated before, I enjoy the speed at which I can prototype an idea in Ruby. Even if ultimately that idea runs better on another language. For the sake of my academics, it provides a super learning playground. 

Earlier, this past week, I began reading up on papers and documentation of trees. All in an effort to differentiate the various implementations of trees, including their benefits and drawbacks. Finding AVL trees, B trees, 2-3 node, 2-3-4 node and RB trees. I decided to kick off the events with a Red-Black tree. Some of the papers I found are the [generally available by Google searching][1]. 

After a lot of learning, and a little massage, I have the first of a an &#8220;academic&#8221; implementation of an RB tree (<http://hashbangbash.com/~vbatts/rbtree.rb>). I gravitated towards this tree first because of the type searches I am needing. While the population of this slows as it grows, the searches are ridiculously fast. I&#8217;ll get around to generating some instrumentation to graph it. For now I have the run_loop(<int>) to return @results of the populating trees. The sets are [ <number of nodes>, <seconds>, <deepest left most left>, <deepest rightmost leg>] 

The insertion graphed was an O(N) climb. For doubling the number of nodes to be inserted (of random unique numbers), the time increase was just a tad more than double. The @results for run_loop(1,000,000) was
  
`=> [[1000000, 43.4912700653076, 24, 16], [500000, 20.5859882831573, 20, 15], [250000, 9.5372850894928, 20, 14], [125000, 4.50037479400635, 21, 13], [62500, 2.01573848724365, 16, 12], [31250, 0.941669225692749, 14, 11], [15625, 0.435366868972778, 14, 11], [7812, 0.252951860427856, 13, 10], [3906, 0.0940215587615967, 11, 9], [1953, 0.043536901473999, 13, 8], [976, 0.0200107097625732, 9, 8], [488, 0.00921034812927246, 9, 6], [244, 0.00417494773864746, 10, 5], [122, 0.00190210342407227, 6, 5], [61, 0.000821590423583984, 5, 4], [30, 0.000351667404174805, 4, 3], [15, 0.000170707702636719, 4, 2], [7, 6.03199005126953e-05, 2, 1], [3, 2.57492065429688e-05, 1, 1]]` 
  
which resulted in a time climb of 
  
[<img src="/img/2011/02/graph1-300x263.png" alt="" title="graph1" width="300" height="263" />][2]
  

  
Another interesting observation was the logarithmic oscillations of the depth of the left most leg. 
  
[<img src="/img/2011/02/graph2-300x170.png" alt="" title="graph2" width="300" height="170" />][3] 

At any rate, enjoy and feel free to send your thoughts.
  
Take care,
  
vb

 [1]: http://www.google.com/search?q=red+black+trees+filetype:pdf
 [2]: /img/2011/02/graph1.png
 [3]: /img/2011/02/graph2.png