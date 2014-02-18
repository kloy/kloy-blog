---
layout: post
title: "Testing REST API with Behat"
date: 2012-05-02 08:17
---

Today I'll cover testing REST APIs with [Behat](http://behat.org/). I will be using
[Laravel](http://laravel.com/) to build our sample REST API. Also, I will be using guzzle
as the REST client. This proves to be a much simpler method of testing the REST API then
writing a driver or extending mink.

This is more of an advanced tutorial so I'm going to skip over some of the more basic
setup steps or just briefly mention them. If you need additional setup help just ask in
the comments.

An example containing all of the code needed can be found on github at
[behat-rest-testing](https://github.com/kloy/behat-rest-testing).


### Getting Started

Before we start we need to git a fresh copy of [Laravel](http://laravel.com/).

{% highlight bash %}
# Clone laravel repo
git://github.com/laravel/laravel.git
{% endhighlight %}

You will also need to setup a vhost with a local url of **foo.local** that points to the **public**
dir of the laravel project.

Now that we have a fresh project go ahead and cd into it and grab composer.

{% highlight bash %}
# Install composer
curl -s http://getcomposer.org/installer | php
{% endhighlight %}

Awesome, now we are ready to setup our **composer.json** with the necessary
dependencies. Go ahead and create **composer.json** at the top of our project and add the
following to it.

{% highlight json %}
{
  "require": {
    "behat/behat": "2.3.*",
    "guzzle/guzzle": "2.4.*"
  },
  "config": {
    "bin-dir": "bin/"
  }
}
{% endhighlight %}

Now that we have our dependencies listed go ahead and install them.

{% highlight bash %}
# install composer
php composer.phar install
{% endhighlight %}

You should now have a few libraries in **vendor/** and an executable for **behat** in
**bin/**. We can now go ahead and initialize behat and get to working on our custom REST
Context.

{% highlight bash %}
# initialize behat
bin/behat --init
{% endhighlight %}

### Writing our REST Context

Create a file in **features/bootstrap/** called **RestContext.php**. Add the following to
**RestContext.php**...

{% gist 9065468 %}

So that was a bit of code. I'll sort of explain it in a moment after we get it working. In
order for these steps to work we need to add **RestContext** as a sub context to **FeatureContext**.
Let's do that.

{% gist 9065426 %}

So what we did is require our RestContext.php file and instantiate it in
`$this->useContext('RestContext', new RestContext($parameters));`. Now when you type
**bin/behat -dl** you should see a list commands that look like...

{% gist 9065415 %}

All of these steps are coming from our RestContext class.

### Adding behat.yml

We need a config file for behat specifying where our api is. Create the file **behat.yml**
at the top dir of your project.

{% highlight yaml %}
# behat.yml
default:
  context:
    parameters:
      base_url: http://foo.local
{% endhighlight %}

The property **base_url** is where we specified the location of our api.

### Writing our features

Now that all the ground work is in place, let's write a few features for testing. Go ahead
and create a file **features/user.feature** and add the following to it.

{% gist 9065379 %}

Now that we have our feature let's run it with **bin/behat features/user.feature**. You should
get errors with a large amount of html spit back out at you. This is because Laravel
does not have a route/api for what is being tested. We will create that in the next step.

### Creating our API

Our API is for demonstration purposes only, so I'm going to go ahead and provide a quick
stub API that will pass our expectations. Enter the following code into **application/routes.php**.

{% gist 9065480 %}

Go ahead and run **bin/behat features/user.feature** and you should get all green with a
message like so...
{% gist 9065398 %}

### Closing notes

Now that we have a REST API in place and a Rest Context for behat you should be able to
see how we could test any REST API. If you need more steps to use when testing your REST
APIs just add them to the RestContext class and they will then be available to use in your
Gherkin features.

### Credits

This article was heavily inspired by
[behat + fuelphp = restful testing happiness](http://blog.phpdeveloper.org/?p=456) written
by Chris Cornutt. The RestContext class is just a refactor of his FeatureContextRest class
to allow using it as a sub context which I feel makes it more natural to use and easier to
reuse in projects. I also wanted to make a point that these principals apply to any REST API and
not just ones built with FuelPHP.