---
layout: post
title: "Logrotate utility on Linux"
description: "How to rotate logs using logrotate"
modified: 2014-05-30 14:37:56 +1000
category: articles
tags: [linux,log, centOS 6.5]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

Logrotate  is  designed to ease administration of systems that generate large numbers of log files.  It allows automatic rotation, compression, removal, and mailing of log files.   Eachlog file may be handled daily, weekly, monthly, or when it grows too large.


* Rotate the log files when the size reaches to a specified size.
* Rotate the log files daily/ weekly/ monthly basis.
* Compress the rotated log files.
* Specify compression option for the rotated log files.
* Rotate the log files with date.
* Remove old rotated log files .

###Basic Info about comment:

* Location of Command:  ```/usr/sbin/logrotate```
* Location of configuration File: ```/etc/logrotate.conf```
* Location of configuration paht for individual application log: ```/etc/logrotate.d```
* Script executes the logrotate command everyday: ```/etc/cron.daily/logrotate```

How to Use this great tool:

If you wish to use logrotate utility for a specific application you must add a log file to logrotate utility, and then you will need to
create a file inside ```/etc/logrotate.d/``` folder.

Some examplet are listed below :

**Example 1:** Yum Log rotation:

{% highlight text linenos %}
cat /etc/logrotate.d/yum

	/var/log/yum.log {
    	missingok
    	notifempty
    	size 30k
    	yearly
    	create 0600 root root
}
{% endhighlight %}

**Example 2:** Rotate the log file when file size reaches a specific limit:

If you want to rotate a log file (for example, /tmp/mylog.log) for every 100MB, create the logrotate.conf as shown below.

{% highlight text linenos %}
vi /etc/logrotate.d/mylog

	/var/log/mylog.log {
    	size 100M
    	create 0700 root root
    	dateext
    	rotate 4
}
{% endhighlight %}

This logrotate configuration has following three options:

* size 100M – logrotate runs only if the filesize is equal to (or greater than) this size.
* create – rotate the original file and create the new file with specified permission, user and group.
* rotate – limits the number of log file rotation. So, this would keep only the recent 4 rotated log files.
* dateext - rotate log file with date in the log file.

To see more options that logrotate supports see the man page.

#Testing 

Write someting to log file :

{% highlight bash linenos %}sudo dd if=/dev/urandom of=/var/log/mylog.log bs=100M count=1{% endhighlight %}

Make sure that log file size is bigger or euqal to 100M.

To rotate the log file.
{% highlight bash linenos %}
cd /etc/logrotate.d
sudo logrotate -s /var/log/mylog.log mylog
{% endhighlight %}

To confirm that mylog.log has been rotated.

{% highlight bash linenos %}
cd /var/log
ls -ltr |grep mylog.log

-rw-r--r--. 1 root   root   67108864 May 30 15:46 mylog.log-20140530
-rwx------. 1 root   root        0 May 30 15:48 mylog.log
{% endhighlight %}

As you can see above ```mylog.log``` has been rotated with date applyed at the end.
This would only work once in a day becouse of the date tag will be the same, if you want to have multiple logs with in the day remove 
```dateext``` and let it rotate automaticly with adding a number like in this example ```mylog.log.1``` 


