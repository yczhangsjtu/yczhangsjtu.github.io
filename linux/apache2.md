---
title: Introduction to Apache2
---

# Apache2

Apache2 is a most popular web server on the Linux platform.
Once launched, it runs as a daemon underground.

## Installation
Apache2 can be directly installed from command line by ```sudo apt-get install apache2```
Wait a while and apache2 is installed automatically.

## Launch
The arguments apache2 requires to be started is so complex that usually we do not launch apache2 directly from the command line by typing
```apache2```.
In fact, we usually use ```apache2ctl``` to launch apache2 instead.
Simply type <xmp>sudo apache2ctl start</xmp> in the command line.
Here the <font face="Courier">start</font> is one of the subcommands of apache2ctl.
Another frequently used subcommand of apache2ctl is <font face="Courier">restart</font>.
<br>
Input <font face="Courier">http://localhost</font> in the address bar of web browser.
You will see the web page offered by apache2.

## Configuration
By default the web page rendered by apache2 lies in the directory <font face="Courier">/var/www</font>.
That is, the default root of the web server is <font face="Courier">/var/www</font>,
which means when you type <font face="Courier">http://localhost/</font> in your own computer,
or <font face="Courier">http://192.168.0.1</font> in another computer (here we assume that your IP address is 192.168.0.1),
the page you obtained is actually <font face="Courier">/var/www/index.html</font>.
<br><br>
If you want to use another directory as the root directory for your website, you have to
<ul>
<li><b>Change the default.conf file(s).</b></li>
Those files lie in <font face="Courier">/etc/apache2/sites-available</font>.
Just change all the appearanced of <font face="Courier">/var/www</font> into your prefered root directory.
<li><b>Change the apache2.conf file.</b></li>
At the bottom of this configuration file are directories that are allowed to be accessed via the HTTPD server.
You can add your root directory into them or simply replace the <font face="Courier">/var/www</font> by your directory.
</ul>
Note: Do NOT add a slash at the end of the directory paths.
<br><br>
After all that, restart apache2 by <xmp>sudo apache2ctl restart</xmp>
Put your own homepage into the new root directory, and visit <font face="Courier">http://localhost</font> via your browser.
You will see that the page rendered by the browser is exactly your own homepage.
