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
Simply type `sudo apache2ctl start` in the command line.
Here the `start` is one of the subcommands of apache2ctl.
Another frequently used subcommand of apache2ctl is `restart`.

Input `http://localhost` in the address bar of web browser.
You will see the web page offered by apache2.

## Configuration
By default the web page rendered by apache2 lies in the directory `/var/www`.
That is, the default root of the web server is `/var/www`,
which means when you type http://localhost/ in your own computer,
or http://192.168.0.1 in another computer (here we assume that your IP address is 192.168.0.1),
the page you obtained is actually `/var/www/index.html`.

If you want to use another directory as the root directory for your website, you have to

* **Change the default.conf file(s).**
Those files lie in `/etc/apache2/sites-available`.
Just change all the appearanced of `/var/www` into your prefered root directory.
* **Change the apache2.conf file.**
At the bottom of this configuration file are directories that are allowed to be accessed via the HTTPD server.
You can add your root directory into them or simply replace the `/var/www` by your directory.

Note: Do NOT add a slash at the end of the directory paths.

After all that, restart apache2 by `sudo apache2ctl restart`
Put your own homepage into the new root directory, and visit `http://localhost` via your browser.
You will see that the page rendered by the browser is exactly your own homepage.
