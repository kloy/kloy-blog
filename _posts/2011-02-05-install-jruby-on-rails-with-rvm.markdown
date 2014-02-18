---
layout: post
title: "Install jRuby on Rails with RVM"
date: 2011-02-05 20:57
---

Recently I was asked to switch to using Rails for a new project at my job. At my company we have
had nightmares deploying our PHP applications on multiple customers environments, therefore I was
asked to use jRuby instead of Ruby so that we may deploy with the JVM. In my research I came
across [RVM](http://rvm.beginrescueend.com/) (Ruby Version Manager), a great app
for managing multiple version of Ruby and/or jRuby on a single machine. In the following
tutorial I will demonstrate how to install RVM and then use it to install jRuby and create
a gem set. Finally we will install Rails and launch a development server to demonstrate it
working.


### A few things to know

When going through this tutorial, the following information is important. Whenever you see
the symbol "$" this denotes the following text is a command to type in terminal. My text
editor of choice is Textmate, so in my commands you will see "mate" sometimes. Feel free to
replace "mate" with "nano", "vim", or whichever your editor of choice might be. Also, I am
on OS X, so if you are using another Unix OS, replace my instructions to edit ".profile"
with ".bashrc" or equivalent.

### Step 1 :: Install RVM (Ruby Version Manager)

*Install RVM*
{% highlight bash %}
$ bash < <( curl http://rvm.beginrescueend.com/releases/rvm-install-head )
{% endhighlight %}


*Open .profile*
{% highlight bash %}
$ mate ~/.profile
{% endhighlight %}

*Insert the following function at the bottom of the .profile file and save*

{% highlight bash %}
# This loads RVM into a shell session.
[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm"
{% endhighlight %}

*Now verify RVM is properly installed*
{% highlight bash %}
$ type rvm | head -1
{% endhighlight %}

*You should see the following printed in terminal*
`rvm is a function`

### Step 2 :: Install jRuby

*Install jRuby with RVM*
{% highlight bash %}
$ rvm install jruby-1.5.6
$ rvm jruby-1.5.6
{% endhighlight %}

*Check ruby version in terminal*
{% highlight bash %}
$ ruby -v
{% endhighlight %}

### Step 3 :: Create and set default gem set

A gem set is a collection of gems you may have for a particular project. The gem set is associated with the current version of
Ruby being used by RVM. Gem sets are useful for having complete control over which versions of gems you use for each project.

*Create a gemset*
{% highlight bash %}
$ rvm --create use jruby-1.5.6@example
{% endhighlight %}

*Set new gemset to the default RVM uses*
{% highlight bash %}
$ rvm --default use jruby-1.5.6@example
{% endhighlight %}

### Step 4 :: Install Rails

**Note DO NOT RUN GEM COMMANDS AS SUDO.** Sudo is different user and circumnavigates where gems are stored relative to where RVM places them.

*Install Rails*
{% highlight bash %}
$ gem install rails --version 3.0.3
{% endhighlight %}

### Step 5 :: Create Rails project

*Create new Rails project and switch to it*
{% highlight bash %}
$ rails new demo
$ cd demo
{% endhighlight %}

### Step 6 :: Modify Gemfile

*Edit the Gemfile*
{% highlight bash %}
$ mate Gemfile
{% endhighlight %}

*Replace the contents of Gemfile with the following.*

{% highlight ruby %}
source 'http://rubygems.org'

gem 'rails', '3.0.3'

if defined?(JRUBY_VERSION)
  gem 'jdbc-sqlite3', '3.6.14.2.056'
  gem 'activerecord-jdbc-adapter', '1.1.1'
  gem 'activerecord-jdbcsqlite3-adapter', '1.1.1'
  gem 'jruby-openssl', '0.7.3'
else
  gem 'sqlite3-ruby', :require => 'sqlite3'
end
{% endhighlight %}

### Step 7 :: Install gems with bundle

*Install all of the gems in your Gemfile*
{% highlight bash %}
$ bundle install
{% endhighlight %}

### Step 8 :: Startup WEBrick and checkout your app!

*Start a rails development server*
{% highlight bash %}
$ rails server
{% endhighlight %}

Load a browser and navigate to http://localhost:3000 to checkout your dummy app.
