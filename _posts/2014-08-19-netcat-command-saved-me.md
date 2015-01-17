---
layout: post
title: "Netcat command saved me"
description: "Netcat the saviour of the day"
category: articles
tags: [linux]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

Netcat has saved my life on number occasions.

## Scan for open ports
{% highlight bash %}nc -z -v -n ipaddress_of_server 20-50{% endhighlight %}

  This should also work in both TCP and UDP mode, I belive you need to use -u option for UDP more.

 <b>-z</b> -- Specifies that nc should just scan for listening daemons, without sending any IO.<br>
 <b>-v</b> -- Have nc give more verbose output.<br>
 <b>-n</b> -- No DNS or service lookups on any specified addresses, hostnames or ports.<br>

 Above command will print al open ports between 20 and 50.

#### Lets have a chat between 2 servers.

I was cought out once that i was not able to copy some config files from host A to host B tue to restriction and what not. So waht batter way to get around then to use Netcat.

Server A (Assume you have ssh to a server with putty or Terminal)
{% highlight bash %}nc -l 2349{% endhighlight %}

Let me explain:

<b>-l</b> -- Tells nc that is shold listen for an incoming connetion.<br>
<b>2349</b> -- Port that nc on server will listen on.

Client B (Assume you have ssh to a server with putty or Terminal)
We now initiate the connection to Server A

{% highlight bash %}nc ServerA_IP 2349{% endhighlight %}

Let me explain:

<b>ServerA_IP</b>-- This is the ipadress of the Server A eg (192.168.0.3)<br>
<b>2349</b>--  This is the PORT that  you have opened on Server A and that  you want to connect to.

At this stage you should be connected to Server A on port 2349. Just start typing and you will see your message displayed on server A and Client B.

## File Transfer

Is the same principal as above excapt command switch is different see below.

Server A (Assume you have ssh to a server with putty or Terminal)

{% highlight bash %}nc -l 2349 < filename.txt{% endhighlight %}
<b>< filename.txt</b>-- Is the file that you want to transfer to Client B

Client B (Assume you have ssh to a server with putty or Terminal)

{% highlight bash %}nc -n ServerA_IP 2349 > filename.txt{% endhighlight %}
<b>> filename.txt</b>-- Is the file that you want to receive from Server A.

That is it.

GO and have a  BEER now.
