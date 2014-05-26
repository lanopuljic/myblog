---
layout: post
title: Unable to fetch my node definition
description: "Unable to fetch my node definition but the agent run will continue"
modified: 2014-05-26 09:21:51 +1000
category: articles
tags: [puppet, error]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

{% highlight text %}
Warning: Unable to fetch my node definition, but the agent run will continue:
Warning: SSL_connect returned=1 errno=0 state=SSLv3 read server session ticket A: sslv3 alert certificate revoked
Info: Retrieving plugin
Error: /File[/var/lib/puppet/lib]: Failed to generate additional resources using 'eval_generate': SSL_connect returned=1 errno=0 state=SSLv3 read server session ticket A: sslv3 alert certificate revoked
Error: /File[/var/lib/puppet/lib]: Could not evaluate: SSL_connect returned=1 errno=0 state=SSLv3 read server session ticket A: sslv3 alert certificate revoked Could not retrieve file metadata for puppet://puppetmaster01.xonal.dev.com/plugins: SSL_connect returned=1 errno=0 state=SSLv3 read server session ticket A: sslv3 alert certificate revoked
Error: Could not retrieve catalog from remote server: SSL_connect returned=1 errno=0 state=SSLv3 read server session ticket A: sslv3 alert certificate revoked
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
Error: Could not send report: SSL_connect returned=1 errno=0 state=SSLv3 read server session ticket A: sslv3 alert certificate revoked
{% endhighlight %}

### Soltution

Logon to agent node

{% highlight bash linenos %}rm -Rf /var/lib/puppet/ssl/*{% endhighlight %}

{% highlight text linenos %}
    puppet agent -t
	Info: Creating a new SSL key for test.xonal.dev.com
	Info: Caching certificate for ca
	Info: csr_attributes file loading from /etc/puppet/csr_attributes.yaml
	Info: Creating a new SSL certificate request for test.xonal.dev.com
	Info: Certificate Request fingerprint (SHA256): F1:78:4A:F0:A3:C4:A6:A5:35:D3:78:2B:FB:EE:5A:E7:0D:A3:B0:E7:37:14:34:2B:E6:A4:87:2F:E3:A1:F2:4D
	Info: Caching certificate for ca
	Exiting; no certificate found and waitforcert is disabled
	{% endhighlight %}

###Go to Puppet Server 

Check if you have new certificate for the node you are trying to fix.
{% highlight text linenos %} puppet cert list {% endhighlight %}

If new certificate is present, you now need to sign it.
{% highlight text linenos %}puppet cert sign server name{% endhighlight %}

### Go back to agent node server and run:

{% highlight text linenos %} puppet agent -t{% endhighlight %}

<h4>Related Posts</h4>
<ul>
  {% for post in site.related_posts limit:5 %}
  <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


