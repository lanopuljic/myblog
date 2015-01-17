---
layout: post
title: "Monitor files with inotify"
description: "Monitor files with inotify in Linux"
category: articles
tags: [linux]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

###Monitor files with inotify in Linux

Inotify (inode notify) is a Linux kernel subsystem that acts to extend filesystems to notice changes to the filesystem, and report those changes.


First we will need to install inotify tools from EPEL repository.

{% highlight bash %}sudo yum --enablerepo=epel -y install inotify-tools{% endhighlight %}

When you have installed inotify tools  you may try to use them.

Start inotify and tell it what to monitor.

{% highlight bash %}inotifywait -e create,delete,modify,move -o /var/log/inotify.log -mrq /tmp & {% endhighlight %}

Lets break it down.
{% highlight text %}
-e --> Tells inotify what even to monitor we used [create, delete, modify, move]<br>
-o --> This will output the events to a log file, in my case i used /var/log/inotify.log<br>
-m --> This is tells inofify to monitor<br>
-r --> Tell inotify to monitor directories recursively<br>
-q -->  This is used for quiet mode basicly less gitter.<br>

For more information see man inotifywait
{% endhighlight %}

Test and see if we did this right.

Change directory to /tmp

{% highlight bash %}cd /tmp{% endhighlight %}

Now we need to create a file

{% highlight bash %}touch foo.bar{% endhighlight %}

In this instance you wont see anything because we are outputing everything to a log file. So lets check the file.

{% highlight bash %}cat /var/log/inotify.log{% endhighlight %}

You should see something like this.

```tmp/ CREATE foo.bar```

Play with is by deleting or renaming it or even editing it and see what happens.
