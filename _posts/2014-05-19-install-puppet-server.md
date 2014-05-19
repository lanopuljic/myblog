---
layout: post
title: "Install Puppet Server on centOS 6.5"
modified: 2014-05-19 09:36:56 +1000
tags: [puppet,centos]
image:
  feature: 
  credit: 
  creditlink: 
comments: 
share: 
---

# Download puppet-server from Puppet Labs
```yum install -y puppet-server```
#### Start Puppet-Server
```/etc/init.d/puppetmaster start```

#### Set Puppet Master to run on startup ( if you usng passenged no need to do this)
```puppet resource service puppetmaster ensure=running enable=true```

#### Download apache and necessary dependencies
```yum install -y httpd httpd-devel mod_ssl ruby-devel rubygems gcc-c++ curl-devel zlib-devel make automake openssl-devel```
 
#### Install Rack/Passenger
```gem install rack passenger
passenger-install-apache2-module ```

Add passenger to be loaded from http.conf gloabaly

```vi etc/http/conf/http.con```

add this in Load Module section

```LoadModule passenger_module /usr/lib/ruby/gems/1.8/gems/passenger-4.0.42/buildout/apache2/mod_passenger.so
PassengerRoot /usr/lib/ruby/gems/1.8/gems/passenger-4.0.42
PassengerDefaultRuby /usr/bin/ruby```
 
#### Create the directory structure for Puppet Master Rack Application
mkdir -p /usr/share/puppet/rack/puppetmasterd
mkdir /usr/share/puppet/rack/puppetmasterd/public /usr/share/puppet/rack/puppetmasterd/tmp
cp /usr/share/puppet/ext/rack/config.ru  /usr/share/puppet/rack/puppetmasterd/
chown puppet:puppet /usr/share/puppet/rack/puppetmasterd/config.ru

Create a virtual host file for puppet:

```vi /etc/httpd/conf.d/puppetmaster.conf```

Add Following in puppetmaster.conf

RHEL/CentOS:

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

Start up Apache:
 
```/etc/init.d/puppetmaster stop
/etc/init.d/httpd start```

If you get follwoing meesage

Starting httpd: httpd: Could not reliably determine the server's fully qualified domain name, using vlxpuppet01.qbe.com.au for ServerName

```edit the /etc/hosts file and add the server and ip address.```

Disable WEBrick: ( No need to do this if you didnt demonize the process in above step)
```chkconfig puppetmaster off```

```Enable Apache on boot
chkconfig httpd on 34```

Make sure the port is open and it’s listening:

```netstat -ln | grep 8140
 
tcp    0  0 0.0.0.0:8140         0.0.0.0:*              LISTEN```
