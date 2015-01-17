---
layout: post
title: "Installing Ruby on Rails"
description: "How to Install Ruby on Rails"
category: articles
tags: [RoR, Ruby on Rails, CentOS 6.5]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

Linux Distribution : CentOS 6.5

####First we need to create a deploy user and set password that we will be using.
This user will be used to deply the website and in same time we will use this user to run the website as.
{% highlight bash linenos %}adduser deploy{% endhighlight %}
Set the password for **deploy** user
{% highlight bash linenos %}passwd deploy{% endhighlight %}

Also make sure you add ```deploy``` user in to sudoers file.

{% highlight bash linenos %}
sudo vi /etc/sudoers
deploy	ALL=(ALL) NOPASSWD: ALL
{% endhighlight %}

####Make sure we have installed all dependencies

In many cases you should have Fedora EPEL repository configured and read
{% highlight bash linenos %}
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
sudo rpm -Uvh epel-release-6*.rpm
{% endhighlight %}

We need to enable remi repository:
{% highlight bash linenos %}vi /etc/yum.repos.d/remi.repo{% endhighlight %}
In ```[remi]``` section make sure you set **enabled option to 1**

Now we will install some packages.
{% highlight bash linenos %}yum install readline-devel zlib zlib-devel libyaml-devel libffi-devel  make bzip2 autoconf automake libtool bison sqlite-devel libxml2 libxml2-devel libxslt libxslt-devel libtool curl-devel{% endhighlight %}


####RVM, Ruby and Rails
We will use [RVM](http://rvm.io/rvm/install) - commend line tool which will make life lot easier when we need to manage multiple versions of Ruby.
To install RVM.

{% highlight ruby linenos %}\curl -L https://get.rvm.io | bash -s stable{% endhighlight %}

Read the output and make sure that you do everything that tells you if everything is okay run,

**Note:** Output may vary so make sure you read what the output says.

{% highlight bash linenos %}
source /etc/profile.d/rvm.sh
source ~/.bash_profile
{% endhighlight %}

Now to make sure you add following in ```~/.bash_profile```:
{% highlight text linenos %}
[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm"
PATH=$PATH:$HOME/.rvm/bin # Add RVM to PATH for scripting
{% endhighlight %}

Now to check and make sure that everything was installed correctly:
{% highlight text linenos %}
type rvm |head -1
{% endhighlight %}

If everything was installed correctly you should see ```rvm is a function```.

####Now we can install specified Ruby version:
{% highlight ruby linenos %}rvm install 2.1.2{% endhighlight %}

To check what version you have:
{% highlight ruby linenos %}
ruby -v
ruby 2.1.2p95 (2014-05-08 revision 45877) [x86_64-linux]
{% endhighlight %}

Now that we have successfully installed Ruby, now we can go ahead and install Bundler and Rails.
{% highlight ruby linenos %}gem install bundler rails{% endhighlight %}

To check what version you have:
{% highlight ruby linenos %}
rails -v
Rails 4.1.1
{% endhighlight %}
