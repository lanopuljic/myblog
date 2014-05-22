---
layout: post
title: "Install Puppet Server on CentOS 6.5"
description: "How to install Puppet Server on Centos 6.5"
modified: 2014-05-19 09:36:56 +1000
category: articles
tags: [puppet,centos 6.5]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

#### Download puppet-server from Puppet Labs
{% highlight bash linenos %}yum install -y puppet-server{% endhighlight %}

#### Start Puppet-Server
{% highlight bash linenos %}/etc/init.d/puppetmaster start{% endhighlight %}

#### Set Puppet Master to run on startup ( if you usng passenged no need to do this)
{% highlight bash linenos %}puppet resource service puppetmaster ensure=running enable=true{% endhighlight %}

#### Download apache and necessary dependencies

{% highlight bash linenos %}yum install -y httpd httpd-devel mod_ssl ruby-devel rubygems gcc-c++ curl-devel zlib-devel make automake openssl-devel{% endhighlight %}

    
#### Install Rack/Passenger
{% highlight bash linenos %}
gem install rack passenger
passenger-install-apache2-module
{% endhighlight %}

Add passenger to be loaded from http.conf gloabaly

{% highlight bash linenos %}vi etc/http/conf/http.con{% endhighlight %}


Add this in Load Module section

{% highlight apache %}
LoadModule passenger_module /usr/lib/ruby/gems/1.8/gems/passenger-4.0.42/buildout/apache2/mod_passenger.so
PassengerRoot /usr/lib/ruby/gems/1.8/gems/passenger-4.0.42```
PassengerDefaultRuby /usr/bin/ruby
{% endhighlight %}
 
#### Create the directory structure for Puppet Master Rack Application

{% highlight bash linenos %}
mkdir -p /usr/share/puppet/rack/puppetmasterd
mkdir /usr/share/puppet/rack/puppetmasterd/public /usr/share/puppet/rack/puppetmasterd/tmp
cp /usr/share/puppet/ext/rack/config.ru  /usr/share/puppet/rack/puppetmasterd/
chown puppet:puppet /usr/share/puppet/rack/puppetmasterd/config.ru
{% endhighlight  %}

Create a virtual host file for puppet:

{% highlight bash linenos %}vi /etc/httpd/conf.d/puppetmaster.conf{% endhighlight %}

Add Following in puppetmaster.conf

RHEL/CentOS:
{% highlight apache %}
#### And the passenger performance tuning settings:
PassengerHighPerformance On
#### Set this to about 1.5 times the number of CPU cores in your master:
PassengerMaxPoolSize 12
#### Recycle master processes after they service 1000 requests
PassengerMaxRequests 1000
#### Stop processes if they sit idle for 10 minutes
PassengerPoolIdleTime 600

Listen 8140
<VirtualHost *:8140>
    SSLEngine On

    # Only allow high security cryptography. Alter if needed for compatibility.
    SSLProtocol             All -SSLv2
    SSLCipherSuite          HIGH:!ADH:RC4+RSA:-MEDIUM:-LOW:-EXP
    SSLCertificateFile      /var/lib/puppet/ssl/certs/vlxpuppet01.qbe.com.au.pem
    SSLCertificateKeyFile   /var/lib/puppet/ssl/private_keys/vlxpuppet01.qbe.com.au.pem
    SSLCertificateChainFile /var/lib/puppet/ssl/ca/ca_crt.pem
    SSLCACertificateFile    /var/lib/puppet/ssl/ca/ca_crt.pem
    SSLCARevocationFile     /var/lib/puppet/ssl/ca/ca_crl.pem
    SSLVerifyClient         optional
    SSLVerifyDepth          1
    SSLOptions              +StdEnvVars +ExportCertData

    # These request headers are used to pass the client certificate
    # authentication information on to the puppet master process
    RequestHeader set X-SSL-Subject %{SSL_CLIENT_S_DN}e
    RequestHeader set X-Client-DN %{SSL_CLIENT_S_DN}e
    RequestHeader set X-Client-Verify %{SSL_CLIENT_VERIFY}e

    DocumentRoot /usr/share/puppet/rack/puppetmasterd/public

    <Directory /usr/share/puppet/rack/puppetmasterd/>
      Options None
      AllowOverride None
        Order allow,deny
        Allow from all
    </Directory>

    ErrorLog /var/log/httpd/puppet-server.example.com_ssl_error.log
    CustomLog /var/log/httpd/puppet-server.example.com_ssl_access.log combined
</VirtualHost>
{% endhighlight %}
Start up Apache:
 
{% highlight bash linenos %}
/etc/init.d/puppetmaster stop
/etc/init.d/httpd start
{% endhighlight %}

If you get follwoing meesage

Starting httpd: httpd: Could not reliably determine the server's fully qualified domain name, using vlxpuppet01.qbe.com.au for ServerName

Edit host file and add the server and ip address.
{% highlight bash linenos %}vi /etc/hosts{% endhighlight %}

Disable WEBrick: ( No need to do this if you didnt demonize the process in above step)
{% highlight bash linenos %}chkconfig puppetmaster off{% endhighlight %}

Enable Apache on boot
{% highlight bash linenos %}chkconfig httpd on 34{% endhighlight %}

Make sure the port is open and it’s listening:

{% highlight bash linenos %}
netstat -ln | grep 8140
tcp    0  0 0.0.0.0:8140         0.0.0.0:*              LISTEN
{% endhighlight %}
