---
layout: post
title: "Installing MySQL Server"
description: "How to Install MySQL Server"
category: articles
tags: [MySQL]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

####Installation of MySQL

Install required packages using yum

{% highlight bash linenos %}sudo yum install mysql-server mysql mysql-devel{% endhighlight %}

Now you can Start MySQL server to Initialise the installation of MySQL DB.

{% highlight bash linenos %} sudo /etc/init.d/mysqld start {% endhighlight %}

Once initialization has completed make sure you set the password.

{% highlight bash linenos %}
  /usr/bin/mysqladmin -u root password 'new-password'
{% endhighlight %}

To confirm that password has set correctly we can try logging in to MySQL.

{% highlight bash linenos %}
  mysql -uroot -ppassword
{% endhighlight %}

If you have set the password correctly you should see output similar as the one below.
{% highlight MySQL %}
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.1.73 Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
{% endhighlight %}

If there was no issues you may exit from MySQL by typing ```quit```

#####Last thing installing MySQL and Ruby on Rails

If there was no any issue so far we can assume that RoR (Ruby on Rails) has been installed correctly.

To instally MySQL with RoR we need to install one more paxkage.

{% highlight bash linenos %}
  gem install mysql -- --with-mysql-config=/usr/lib64/mysql/mysql_config
{% endhighlight %}

If no issues we can do a quick check to see if everything went well.

{% highlight ruby linenos %}
irb
2.1.2 :001 > require 'rubygems'
 => false
2.1.2 :002 > require 'mysql'
 => true
2.1.2 :003 >
{% endhighlight %}

If you have output as above you should be ready to deply your app.
From my understanding ```require 'rubygems'```  may return ```false``` but that is because rubygems is already loaded.
