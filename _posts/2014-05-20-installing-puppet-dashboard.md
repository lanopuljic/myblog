---
layout: post
title: Installing Puppet Dashboard CentOS 6.5
description: "How to install Puppet Dashboard on Centos 6.5"
modified: 2014-05-20 23:10:23 +1000
category: articles
tags: [puppet,dashboard,centOS 6.5]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

Install Following Packages

{% highlight bash linenos %}yum -y install puppet-dashboard{% endhighlight %}

Install MySQL

{% highlight bash linenos %}yum -y install mysql-server{% endhighlight %}

Start service on next reboot
{% highlight bash linenos %}
chkconfig mysqld on 3 4
service mysqld start
{% endhighlight %}

Set MySQL root user’s password
{% highlight bash linenos %}
/usr/bin/mysqladmin -u root password 'new-password'
{% endhighlight %}

Create a database and database user:
{% highlight MySQL linenos %}
mysql -u root -p
mysql> CREATE DATABASE dashboard CHARACTER SET utf8;
mysql> CREATE USER 'dashboard'@'localhost' IDENTIFIED BY 'secret_password';
mysql> GRANT ALL PRIVILEGES ON dashboard.* TO 'dashboard'@'localhost';
{% endhighlight %}

Edit following config file and make changes to database,username,password you created in above step.

{% highlight bash linenos %}vi /usr/share/puppet-dashboard/config/database.yml{% endhighlight %}
{% highlight bash %}
production:
database: dashboard
username: dashboard
password: secret_password
encoding: utf8
adapter: mysql
 
development:
database: dashboard
username: dashboard
password: secret_password
encoding: utf8
adapter: mysql
{% endhighlight %}

Populate the database
{% highlight bash linenos %}
cd /usr/share/puppet-dashboard
{% endhighlight %}
{% highlight ruby linenos %}
rake db:migrate
{% endhighlight %}

Copy Apache vhost config file from the example Puppet Dashboard that is provided.

{% highlight bash linenos %}cp /usr/share/puppet-dashboard/ext/passenger/dashboard-vhost.conf /etc/httpd/conf.d/puppetdashboard.conf{% endhighlight %}

Edit the puppetdashboar.conf file and change things to suite your environment.

{% highlight bash linenos %}vi /etc/https/conf.d/puppetdashboard.conf{% endhighlight %}

Example of my configuration file is below.
{% highlight apache linenos %}
# you may want to tune these settings
PassengerHighPerformance on
PassengerMaxPoolSize 12
PassengerPoolIdleTime 1500
# PassengerMaxRequests 1000
PassengerStatThrottleRate 120

Listen 3000
<VirtualHost *:3000>
        ServerName puppetmaster01.xonal.dev.com
        DocumentRoot /usr/share/puppet-dashboard/public/
        <Directory /usr/share/puppet-dashboard/public/>
                Options None
                Order allow,deny
                allow from all
        </Directory>
  ErrorLog /var/log/httpd/dashboard.example.com_error.log
  LogLevel warn
  CustomLog /var/log/httpd/dashboard.example.com_access.log combined
  ServerSignature On

</VirtualHost>
{% endhighlight %}

####Create logs for Dashboard

Make sure that log file exists and permissions are correct.
{% highlight bash linenos %}
touch /usr/share/puppet-dashboard/log/production.log
chmod 0666 /usr/share/puppet-dashboard/log/production.log
{% endhighlight %}

Restart the https service
{% highlight bash linenos %}service httpd restart{% endhighlight %}

If ther is no error you should be able to load puppet dashboard from your browser that looks like this.

<figure>
  <a href="/images/puppet_dashboard.png" target="_blank"><img src="/images/puppet_dashboard.png"></a>
  <figcaption> Puppet Dashboard</figcaption>
</figure>


###Enable inventory support

When you add a node to puppet, Click on the node and you will see **INVENTORY** at first this will not display anything,
if you follow the instruction below you will get inventory working.

In a nutshell, you need to change just a few settings.

Go to settings.yml and change:

{% highlight bash linenos %}vi /usr/share/puppet-dashboard/config/settings.yml{% endhighlight %}

Ensure that /usr/share/puppet-dashboard/config/settings.yaml has following
{% highlight yaml  %}
# Hostname of the certificate authority.
ca_server: 'puppetmaster01.xonal.dev.com' # this is your puppetmaster FQDN

# The "inventory service" allows you to connect to a puppet master to retrieve and node facts
enable_inventory_service: true

# Hostname of the inventory server.
inventory_server: 'puppetmaster01.xonal.dev.com' # this is your puppetmaster FQDN
{% endhighlight %}

Make sure that service puppet-dashboard-workers is started on boot
{% highlight bash linenos %}
chkconfig puppet-dashboard-workers on 34
service puppet-dashboard-workers start
{% endhighlight %}

Last but not lease make sure you have following in you /etc/puppet/puppet.conf

{% highlight bash linenos %}vi /etc/puppet/puppet.conf{% endhighlight %}
{% highlight bash %}
[master]
    reports = store, http
    reportdir = /var/lib/puppet/reports
    reporturl = http://puppetmaster01.xonal.dev.com:3000/reports/upload
{% endhighlight %}

####Configuring inventory to work on Puppet Dashboard

Edit auth.conf file

{% highlight bash linenos %}vi/etc/puppet/auth.conf file{% endhighlight %}

Add follwoing lines.
{% highlight bash linenos %}
path /facts
auth yes
method find, search
allow dashboard

Make sure its above this
path /
auth any
{% endhighlight %}

Generating Certificate for Dashboard so that NODE Inventory will work.

Generate your keypair for dashboard:

{% highlight bash linenos %}sudo -u puppet-dashboard rake cert:create_key_pair{% endhighlight %}

Generate the cert request for dashboard:

{% highlight bash linenos %}sudo -u puppet-dashboard rake cert:request{% endhighlight %}

On the puppetmaster, sign the cert:

{% highlight bash linenos %}puppet cert sign dashboard{% endhighlight %}

Get the cert from the puppetmaster

{% highlight bash linenos %}sudo -u puppet-dashboard rake cert:retrieve{% endhighlight %}

Restart dashboard
{% highlight bash linenos %} service httpd restart{% endhighlight %}

Loging to dashboard and see if it works.

<br>
{% for post in site.related_posts %}
  <a href="{{ post.url }}">{{ post.title }}</a><br />
{% endfor %}