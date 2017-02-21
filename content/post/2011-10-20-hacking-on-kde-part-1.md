---
title: 'Hacking on KDE: part 1'
author: vbatts
layout: post
date: 2011-10-20T14:47:39+00:00
url: /2011/10/hacking-on-kde-part-1/
aktt_notify_twitter:
  - yes
  - yes
aktt_tweeted:
  - 1
  - 1
categories:
  - Community
  - Projects

---
In a personal brainstorm of how to better facilitate folks that want to have an ideal hacking environment, for X. The primitives of the idea need to be figured out, and often nothing happens because you can determine &#8220;If the person has enough desire, and know how, they&#8217;ll figure it out,&#8221; and most of the time this is a fine explanation. 

When it comes to a project collection like <a href="http://kde.org/" title="KDE.org" target="_blank">KDE</a>, compiling it and fiddling around can be supremely easy without the bounds of package dependencies, but still manageable packages. Although, there are now a lot a projects in the KDE Software Collection, and they are all modular. Even within the Slackware community, alienBob has already updated the build process for his <a href="http://alien.slackbook.org/ktown/4.7.2/" target="_blank">KDE SC 4.7 packages to be modular</a>. With the <a href="http://techbase.kde.org/Projects/MovetoGit" target="_blank">migration from SVN to git</a>, it has clarified some aspects of the build, but it has now made a segregated landscape of projects. If someone wants to check out a new project, the latest development in it, it seems like a hidden process.

Being agnostic of which Linux distribution I am running, if, as a developer, I want to be able to easily manage working from the git repositories of the projects, installing them, and possibly even packaging the artifacts, this is a intricate process. But thankfully, it is a describable process, therefore we can automate it.

Last night I began working on a project, tentatively named &#8216;kappy&#8217;. With the K App, in mind, and it is written in Python, so having a py in there seems suitable.
  
The source code is currently living at http://hashbangbash.com/kappy.git, or browser viewable at <a href="http://hashbangbash.com/git/?p=kappy.git;a=summary" title="kappy.git" target="_blank">http://hashbangbash.com/git/?p=kappy.git;a=summary</a>

Initially it is just doing XML parsing of the projects available. Next plans are:

  * making a caching ground for cloning/updating git repositories
  * building the software
  * having user definable configurations for flags and install paths
  * making recipes, of a suite of software to build
  * having a manager to package, for a respective distribution

Update: I&#8217;ve added a bug tracker for this project <a href="http://redmine.hashbangbash.com/projects/kappy" title="Kappy - bugtracker" target="_blank">http://redmine.hashbangbash.com/projects/kappy</a>

If you have feedback, feel free to send it in.

Take care,
  
vb