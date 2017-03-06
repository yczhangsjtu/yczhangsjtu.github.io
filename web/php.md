---
title: PHP - Hypertext Preprocessor
---

# PHP - Hypertext Preprocessor
When you are surfing the internet you probably find some web pages ended
with .html. Generally speaking, when the web page is already sent to
your computer and displayed by your browser, a.html page is not at all
different with a html page. The difference lies in how the server
generated this page.

Suppose you are running a website where there is a webpage telling the
users about the date. It is not a wise choice to use a static html page
like this:
2015-05-24
Admittedly, this webpage does what it is supposed to do, though it is a
bit troubling for you to alter this page every day. What is worse, there
could be some highly demanding users toleranting with no mistake at all,
which means you had better update the date at exactly twelve o'clock.
This is more than troublesome.

Dynamic webpages comes as a solver to all kinds of problems like this.
PHP is one of such kind of webpages. Others include ASP which is
developed by Microsoft. If your web server is running together with a
PHP server, all you need to do to solve this problem is replace the
webpage by the following one
&lt;.html \$date=date('Y-m-d'); echo \$date; ?&gt;
When the client request for this webpage, the webpage is first sent to
the PHP server, which looks for all the &lt;.html ?&gt; pairs that
occurred in the page. For each such pair, the server runs the PHP codes
inside and generates some text, which takes the place of the original
&lt;.html ?&gt;. What is sent to the client looks like a normal webpage,
only that it is generated on the spot rather than stored in the server.

If you are running the web server on a linux system, you are probably
using apache. The combination of apache.html-mysql is a most popular one
on the linux platform. Just execute the following command
```
    sudo apt-get install.html5
```

and all is done automatically for you. Write the PHP code in your
webpage and the effects on the browser is immediate.
## Commonly used PHP functions

### date

date('Y-m-d'); returns a string in the form of "Year-month-date". Here
the 'Y-m-d' is called format string.
### basename,dirname,pathinfo

Assume you have a string
```php
    $path = '/home/user/path/to/file.ext';
```

Then the following PHP command
```php
    echo basename($path),"\n";
    echo basename($path,".ext"),"\n";
    echo dirname($path),"\n";
    echo pathinfo($path,PATHINFO_EXTENSION),"\n";
    echo pathinfo($path,PATHINFO_DIRNAME),"\n";
    echo pathinfo($path,PATHINFO_BASENAME),"\n";
    echo pathinfo($path,PATHINFO_FILENAME),"\n";
```

would produce
```php
    file.ext
    file
    /home/user/path/to
    ext
    /home/user/path/to
    file.ext
    file
```
