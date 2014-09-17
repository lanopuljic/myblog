---
layout: post
title: "Display passowrd policy message."
description: "Display password policy when asked to change password"
category: articles
tags: [linux]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

Echo service module for PAM can be used to displays it's policy to users for the new password.

####We need to create a message that you would like to display to users.

If file doesnt exist you just create a new file.

{% highlight bash %}vi /etc/issue.password{% endhighlight %}

Create a displayed message to users, and add it to ```/etc/issue.password```

My example below.

{% highlight text %}
    ****************************************************************************************
  *   Minimum of 8 characters which MUST consist of AT LEAST the following:              *
  *                                                                                      *
  *       - Minimum of 4 lowercase characters                                            *
  *       - Minimum of 2 uppercase characters                                            *
  *       - Minimum of 1 decimal number                                                  *
  *       - Minimum of 1 non-alphanumeric character (i.e. special characters: eg: !@#$%) *
  *                                                                                      *
  ****************************************************************************************
{% endhighlight %}

####Next we need to edit the ```pam.d/passwd file.```

Add the following line```password   optional     pam_echo.so file=/etc/issue.passwd ``` between <b>"account include system-auth” and “password substack system-auth":

so basically what i mean with above.

{% highlight text %}
account    include      system-auth
password   optional     pam_echo.so file=/etc/issue.passwd
password   substack     system-auth
{% endhighlight %}

If you done everything correctly once you try change the password for a user or if the user is prompted to change the password he/she should be prented with somehing like this.

{% highlight bash %}[root@test ~]# passwd test
Changing password for user test.


****************************************************************************************
*   Minimum of 8 characters which MUST consist of AT LEAST the following:              *
*                                                                                      *
*       - Minimum of 4 lowercase characters                                            *
*       - Minimum of 2 uppercase characters                                            *
*       - Minimum of 1 decimal number                                                  *
*       - Minimum of 1 non-alphanumeric character (i.e. special characters: eg: !@#$)  *
*                                                                                      *
****************************************************************************************
{% endhighlight %}
