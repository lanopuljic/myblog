---
layout: post
title:  "Alert by email when server is up and running"
description: "Alert me by email when a disconnected or unreachable server comes back online"
category: articles
tags: [linux, bash]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

If you are lazy like me to wait for server to come back after a reboot, but would like to be notify when it does here is the simple one liner commend that will save you.

{% highlight bash %}
while ! ping -c1 the_host_down; do sleep 1; done && date | mail -s 'the host is back!' youname@iamawesome.com
{% endhighlight %}
