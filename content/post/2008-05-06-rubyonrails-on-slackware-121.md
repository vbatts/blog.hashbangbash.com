---
title: RubyOnRails on slackware-12.1
author: vbatts
layout: post
date: 2008-05-06T10:33:03+00:00
url: /2008/05/rubyonrails-on-slackware-121/
categories:
  - Community
  - Linux
  - Projects

---
On a bit of a break from school (the month of May), i figured to keep the spirit going and learn a language, so i have been reading up on Ruby and Rails. [The Pragmatic Programmer&#8217;s Guide][1]
  
to have a sandbox for my exploration, i had to get certain aspects of this working on my machine. while ruby-1.8.6 is in the Slackware-12.1 base, the fcgi, mod_fcgi, rubygems and rails was not. so i tasked myself to contribute what I found.

### first

:: fcgi
  
i have made available a SlackBuild to use for fastCGI and mod_fcgi, on <http://batts.mine.nu/pub/src/SlackBuilds/fcgi/>, so for this from your command line do 

<pre><blockquote>
  <code>
  $> svn co http://batts.mine.nu/pub/src/SlackBuilds/fcgi/
  $> cd fcgi/
  $> . fcgi.info &#038;&#038; wget $DOWNLOAD
  $> export $(grep DOWNLOAD README)
  $> sudo sh fcgi.SlackBuild
  $> sudo installpkg /tmp/fgci-2.4.0-i486-1_SBo.tgz
  </code>
  
</blockquote>
</pre>

and include in your /etc/httpd/httpd.conf 

<pre><blockquote>
  Listen 8080
  
</blockquote>
</pre>

### second

:: rubygems
  
rubygems is the _Official_ package manager for Ruby. [Their Website][2]
  
their current stable is 1.1.1, so

<pre><blockquote>
  <code>
  $> wget http://rubyforge.org/frs/download.php/35283/rubygems-1.1.1.tgz
  $> tar xzvf rubygems-1.1.1.tgz
  $> cd rubygems-1.1.1
  $> sudo ruby setup.rb
  $> sudo gem update --system
  </code>
  
</blockquote>
</pre>

i have not cleanly gotten a rubygems SlackBuild going, because the &#8216;setup.rb&#8217; builds and installs to the directory of the ruby excutable used to call it, so if ruby is in $PATH at /usr/bin, thats where it will install. so the &#8211;prefix= and DESTDIR are not as easily utilized. :/

### third

:: rails
  
pretty straight foward, this will install these gems in a ruby sub-directory,

<pre><blockquote>
  <code>
  $> gem install rails --include dependencies
  </code>
  
</blockquote>
</pre>

### fourth

:: httpd stuff
  
add to the /etc/httpd/httpd.conf of /etc/httpd/extra/httpd-vhosts.conf the section for your website, substitute for you own paths.

<pre><blockquote>
  &lt;VirtualHost *:8080>
      DocumentRoot /var/www/htdocs/rails/new_project/public
      ServerName rails.batts.lan
      ErrorLog /var/log/httpd/new_project-error_log
      CustomLog /var/log/httpd/new_project-access_log common
      Options Indexes ExecCGI FollowSymLinks
      RewriteEngine On
      &lt;Directory /var/www/htdocs/rails/new_project/public>
          AllowOverride all
          Options Indexes ExecCGI FollowSymLinks
          Order allow,deny
          Allow from all
      &lt;/Directory>
  &lt;/VirtualHost>
  
</blockquote>
</pre>

and then make your website directory `On a bit of a break from school (the month of May), i figured to keep the spirit going and learn a language, so i have been reading up on Ruby and Rails. [The Pragmatic Programmer&#8217;s Guide][1]
  
to have a sandbox for my exploration, i had to get certain aspects of this working on my machine. while ruby-1.8.6 is in the Slackware-12.1 base, the fcgi, mod_fcgi, rubygems and rails was not. so i tasked myself to contribute what I found.

### first

:: fcgi
  
i have made available a SlackBuild to use for fastCGI and mod_fcgi, on <http://batts.mine.nu/pub/src/SlackBuilds/fcgi/>, so for this from your command line do 

<pre><blockquote>
  <code>
  $> svn co http://batts.mine.nu/pub/src/SlackBuilds/fcgi/
  $> cd fcgi/
  $> . fcgi.info &#038;&#038; wget $DOWNLOAD
  $> export $(grep DOWNLOAD README)
  $> sudo sh fcgi.SlackBuild
  $> sudo installpkg /tmp/fgci-2.4.0-i486-1_SBo.tgz
  </code>
  
</blockquote>
</pre>

and include in your /etc/httpd/httpd.conf 

<pre><blockquote>
  Listen 8080
  
</blockquote>
</pre>

### second

:: rubygems
  
rubygems is the _Official_ package manager for Ruby. [Their Website][2]
  
their current stable is 1.1.1, so

<pre><blockquote>
  <code>
  $> wget http://rubyforge.org/frs/download.php/35283/rubygems-1.1.1.tgz
  $> tar xzvf rubygems-1.1.1.tgz
  $> cd rubygems-1.1.1
  $> sudo ruby setup.rb
  $> sudo gem update --system
  </code>
  
</blockquote>
</pre>

i have not cleanly gotten a rubygems SlackBuild going, because the &#8216;setup.rb&#8217; builds and installs to the directory of the ruby excutable used to call it, so if ruby is in $PATH at /usr/bin, thats where it will install. so the &#8211;prefix= and DESTDIR are not as easily utilized. :/

### third

:: rails
  
pretty straight foward, this will install these gems in a ruby sub-directory,

<pre><blockquote>
  <code>
  $> gem install rails --include dependencies
  </code>
  
</blockquote>
</pre>

### fourth

:: httpd stuff
  
add to the /etc/httpd/httpd.conf of /etc/httpd/extra/httpd-vhosts.conf the section for your website, substitute for you own paths.

<pre><blockquote>
  &lt;VirtualHost *:8080>
      DocumentRoot /var/www/htdocs/rails/new_project/public
      ServerName rails.batts.lan
      ErrorLog /var/log/httpd/new_project-error_log
      CustomLog /var/log/httpd/new_project-access_log common
      Options Indexes ExecCGI FollowSymLinks
      RewriteEngine On
      &lt;Directory /var/www/htdocs/rails/new_project/public>
          AllowOverride all
          Options Indexes ExecCGI FollowSymLinks
          Order allow,deny
          Allow from all
      &lt;/Directory>
  &lt;/VirtualHost>
  
</blockquote>
</pre>

and then make your website directory` 

### fifth

:: rails&#8217;ing it up
  
from here you are kind of own your own,
  
with &#8216;pwd&#8217; as your rails base directory, you can scaffold up a rough framework, in my case i am using a mysql database, so

<pre><blockquote>
  <code>
  $> pwd 
  /var/www/htdocs/rails/
  $> rails -d mysql new_project
  $> cd new_project
  $> vi config/database.yml
  </code>
  
</blockquote>
</pre>

and change the database information to your database host, name, user, and pass.
  
and read the README in that directory, and read the &#8216;./script/generate &#8211;help&#8217; 

also, a gotcha, ensure the space you are usind (test, production, development), matches the RAILS_ENV in the httpd opts.

cheers,

vb

 [1]: http://www.ruby-doc.org/docs/ProgrammingRuby/
 [2]: http://www.rubygems.org/