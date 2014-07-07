---
layout: post
title: "Installing Nginx and Passenger"
description: "How to Install Nginx and Passenger on CentOS 6.5"
category: articles
tags: [RoR, Nginx, Passenger]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

####Installation of Nginx and Passenger

We need to have a http server so taht we can publish our application. In this exmaple i chose to go with Nginx as its fast and lightwaight and eaasy to configure.
{% highlight ruby linenos %}gem install passenger{% endhighlight %}

Then we need to install Nginx with compiled Passenger module:
{% highlight ruby linenos %}rvmsudo passenger-install-nginx-module{% endhighlight %}

You should only be asked 2 question during the Installation :

Automatically download and install Nginx? ```choose  recommended```<br>
Where do you want to install Nginx to? ``` take default```<br>

Now we need to setup our Rails Application

{% highlight bash linenos %}vi /opt/nginx/conf/nginx.conf{% endhighlight %}

Add/Customize to you needs bellow code and add it at the bottom of nginx.conf file.

{% highlight nginx linenos %}

server {
  listen       80;
  server_name  some-example.com;

  root /path-to-your-application/current/public;
  client_max_body_size 128M;
  passenger_enabled on;
  rails_env production;

  location ~ ^/(assets|images|javascripts|stylesheets|system)/ {
    expires max;
    add_header Cache-Control public;
  }
}
{% endhighlight %}

Now we need to creat service script for ease of stoping and starting the NGINX from Service level.

{% highlight bash linenos %}sudo vi /etc/init.d/nginx {% endhighlight %}

{% highlight nginx linenos %}
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemin
#
# chkconfig:   - 85 15
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /opt/nginx/conf/nginx.conf
# pidfile:     /opt/nginx/logs/nginx.pid

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/opt/nginx/sbin/nginx"
prog=$(basename $nginx)

NGINX_CONF_FILE="/opt/nginx/conf/nginx.conf"

lockfile=/var/lock/subsys/nginx

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    configtest || return $?
    stop
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}

force_reload() {
    restart
}

configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
status)
    rh_status
    ;;
condrestart|try-restart)
    rh_status_q || exit 0
;;
*)
echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
exit 2
esac

{% endhighlight %}

Make sure that you make the nginx rvice executable:
{% highlight bash linenos %}sudo chmod +x /etc/init.d/nginx {% endhighlight %}
Now you should be able to execute nginx service.
{% highlight bash linenos %}
sudo service nginx status
{% endhighlight %}

Last thing if you wish to start nginx service on next boot

{% highlight bash linenos %}
sudo /sbin/chkconfig nginx on
{% endhighlight %}
