---
title: Web Client Programming in Perl
---

# Web Client Programming in Perl

Introduction to Web and HTTP
============================

The World Wide Web was developed in 1990 by Tim Berners-Lee at the
Conseil Europeen pour la Recherche Nucleaire (CERN).

In 1993 a graphical interface to the Web, name Mosaic, was developed at
the University of Illinois at Urbana-Champaign.

In 1994 a new interface to the Web called Netscape Navigator came on the
market. Meanwhile, everyone started developing their own web sites.

Behind the Browser
==================

General structure of HTTP requests
----------------------------------

```
Method URI HTTP-version
General header
Request header
Entity header

Entity body
```

Example

```
POST /cgi-bin/query HTTP/1.0
Referer: http://hypothetical.ora.com/search.html
Connection: Keep-Alive
User-Agent: Mozilla/3.0Gold (WinNT; I)
Host: hypothetical.ora.com
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, */*
Content-type: application/x-www-form-urlencoded
Content-length: 47

querytype=subject&queryconst=numerical+analysis
```

General structure of server response
------------------------------------

```
HTTP-version Status-code Reason-phrase
General header
Response header
Entity header

Entity body
```

Example

```
HTTP/1.0 200 OK
Date: Fri, 14 Jan 2016 14:11:51 GMT
Server: Apache/2.0
text/html
Content-length: 327
Last-modified: Fri, 14 Jan 2016 14:11:51 GMT

<title>Sample Homepage</title>
src="/images/oreilly_mast.gif"
```

Details of HTTP
===============

HTTP Transaction
----------------

1.  First, client contacts the server and sends a document request, for
    example

            GET /index.html HTTP/1.0
            

2.  Next, client sends optional header information to inform the server
    of the client’s configuration and document preference.

            User-Agent: Mozilla/1.1N
            Accept: */*
            Accept: image/gif
            

    To end the header section, the clients sends a blank line.

3.  When applicable, the clients sends the data portion of the request,
    whose length is specified in the header.

4.  The server responds with a status line like

            HTTP/1.0 200 OK
            

5.  The server supplies header information to tell the client about
    itself and the requested document.

            Date: Fri, 14 Jan 2016 14:11:51 GMT
            Server: NCSA/1.3
            MIME-version: 1.0
            Content-type: text/html
            Content-length: 1029
            Last-modified: Thursday, 13 Jan 2016 18:15:23 GMT
            

Client request methods
----------------------

-   `GET`: Just retrieve a document.

-   `HEAD`: Just retrieve the header.

-   `POST`: Send data to the server, with content in form of

            username=name&password=123456
            

-   `PUT`: Store the entity-body at the URL.

-   `DELETE`: Remove URL.

-   `TRACE`: View the client’s message through the request chain.

-   `OPTIONS`: Request other options available for the URL.

Server response codes
---------------------

  **Code Range**   **Response Meaning**
  ---------------- -----------------------------------------------------
  100-199          Informational
  200-299          Client request successful
  300-399          Client request redirected, further action necessary
  400-499          Client request incomplete
  500-599          Server errors

  : HTTP Response Codes[]{data-label="tab:http.res.code"}

-   **Informational**

    -   `100 Continue`: The client continue waiting.

    -   `101 Switching Protocols`: The server is switching protocols.

-   **Client request successful**

    -   `200 OK`: Successful.

    -   `201 Created`: A new URL is created.

    -   `202 Accepted`: The request was accepted but not immediately
        acted.

    -   `203 Non-Authoritative Information`: The information is not
        from original server.

    -   `204 No Content`: No entity body.

    -   `205 Reset Content`: The browser should clear the form.

    -   `206 Partial Content`: The server is returning partial data of
        the size requested.

-   **Redirection**

    -   `300 Multiple Choices`: The URL refers to more than one
        resource.

    -   `301 Moved Permanently`: The requested URL is no longer used,
        and new location is specified in `Location` header.

    -   `302 Moved Temporarily`: The requested URL has moved
        temporarily, and new location is specified in `Location`
        header.

    -   `303 See Other`: The requested URL can be found at a different
        URL and should be retrieved by a `GET` on that resource.

    -   `304 Not Modified`: Response to an `If-Modified-Since`
        header, the entity-body is not sent.

    -   `305 Use Proxy`: The requested must be accessed through the
        proxy in the `Location` header.

-   **Client request incomplete**

    -   `400 Bad Request`: Syntax error in client’s request.

    -   `401 Unauthorized`: Not authorized.

    -   `403 Forbidden`: The request was denied.

    -   `404 Not Found`: The document at the specified URL does not
        exist.

    -   `405 Method Not Allowed`: The method used is not supported by
        this URL.

    -   `406 Not Acceptable`: The document is not in a formate
        preferred by the client.

    -   `407 Proxy Authentication Required`: The proxy server needs to
        authorize the request.

    -   `408 Request Time-out`: The client did not produce a full
        request within predetermined time.

    -   `409 Conflict`: The request conflicts with another request or
        with the server’s configuration.

    -   `410 Gone`: The URL no longer exists.

    -   `411 Length Required`: The server will not accept the request
        without a `Content-type`.

    -   `412 Precondition Failed`: The condition specified by `If`
        headers in the request evaluated to false.

    -   `413 Request Entity Too Large`: Entity body too large.

    -   `414 Request Too Long`: The requested URL is too long.

    -   `415 Unsupported Media Type`: Entity-body is in an unsupported
        format.

-   **Server error**

    -   `500 Internal Server Error`: Part of the server has crashed.

    -   `501 Not Implemented`: The requested action cannot be
        performed by server.

    -   `502 Bad Gateway`: The server encountered invalid responses
        from another server.

    -   `503 Service Unavailable`: The service is temporarily
        unavailable.

    -   `504 Gateway Time-out`: A gateway or proxy has timed out.

    -   `505 HTTP Version Not Supported`: The server does not support
        the HTTP version used in the request.

HTTP headers
------------

-   **General Headers**: `Cache-Control`, `Connection`, `Date`,
    `MIME-version`, `Pragma`, `Transfer-Encoding`, `Upgrade`,
    `Via`

-   **Request Headers**: `Accept`, `Accept-Charset`,
    `Accept-Encoding`, `Accept-Language`, `Authorization`,
    `Cookie`, `From`, `Host`, `If-Modified-Since`, `If-Match`,
    `If-None-Match`, `If-Range`, `If-Unmodified-Since`,
    `Max-Forwards`, `Proxy-Authorization`, `Range`, `Referer`,
    `User-Agent`

-   **Response Headers**: `Accept-Ranges`, `Age`,
    `Proxy-Authenticate`, `Public`, `Retry-After`, `Server`,
    `Set-Cookie`, `Vary`, `Warning`, `WWW-Authenticate`

-   **Entity Headers**: `Allow`, `Content-Base`,
    `Content-Encoding`, `Content-Language`, `Content-Length`,
    `Content-Location`, `Content-MD5`, `Content-Range`,
    `Content-Transfer-Encoding`, `Content-Type`, `Etag`,
    `Expires`, `Last-Modified`, `Location`, `URI`.

Persistent Connections
----------------------

Persistent connections mean that the network connection remains open
during multiple transactions between client and server. In HTTP 1.0, the
client must use the following header to maintain the connection for an
additional request:

    Connection: Keep-Alive

Under HTTP 1.1, persistent connections are default, so clients does not
need the `Keep-Alive` parameter, yet clients must be sure to include
the following header in their last transaction:

    Connection: Close

Media Types
-----------

For negotiating different data types, HTTP incorporated Internet Media
Types, which look a lot like MIME types but are not exactly MIME types.

The client tells the server which types it can handle, using the
`Accept` header. The server tries to return information in a preferred
media type, and declares the type of the data using the `Content-Type`
header.

    Accept: */*

means the client can accept any media type.

    Accept: image/*

means the client accepts any form of image. While

    Accept: image/gif

means the client accepts only `gif` image.

Client caching
--------------

For caching management, HTTP provides a whole set of headers. There are
two general systems: one based on the age of the document, and the other
one based on unique identifiers for each document.

In HTTP 1.0, the `Pragma: no-cache` header tells caching proxies and
clients not to cache the document. Under HTTP 1.1, the `Cache-Control`
header supplants `Pragma`, with several caching directives in addition
to `no-cache`.

The client can use `If-Modified-Since` header with the `GET` method.
When using this option, the client requests the server to send the
requested information associated with the URL only if it has been
modified since a client-specified time. Example:

    If-Modified-Since: Fri, 15-Jan-2016 06:52:20 GMT

If the server returns code 304, the client could use the cached version
of the document. If the document is newer, the server will send it along
with a 200 code.

In HTTP 1.1, entity tags are unique identifiers that can be associated
with all copies of the document. If the server is using entity tags, it
sends the document with the `ETag` header. When the client wants to
verify the cache, it uses the `If-Match` or `If-None-Match` headers
to check against the entity tag for that resource.

Retrieving document
-------------------

There are three common ways that a client can retrieve data from the
entity-body of the server’s response:

-   The first way is to get the size of the document from the
    `Content-length` header, and then read in that much data.

-   When the size of the document is too dynamic for a server to
    predict, the `Content-length` header is omitted. The client reads
    in the data portion of the server’s response until the server
    disconnects the network connection.

-   Another header could indicate when an entity-body ends, like HTTP
    1.1 `Transfer-Encoding` header with the chunked parameter.

In HTTP 1.1, the client does not have to get the entire entity-body at
once, but can get it in pieces, if the server allows by the
`Accept-Ranges` header.

    HTTP/1.1 200 OK
    Accept-Ranges: bytes

then the client can request the data in pieces, like so:

    GET /largefile.html HTTP/1.1
    Range: 0-65535

When the server returns the specified range, it includes a
`Content-Range` header to indicate which portion of the document is
being sent, and also how long the file is.

    HTTP/1.1 200 OK
    Content-range: 0-65535/83028576

Referring documents
-------------------

The `Referer` header indicates which document referred to the one
currently specified in this request. For example, if the client sends
[www.ora.com](www.ora.com) the following

    GET /contact.html HTTP/1.0
    Accept: */*

the server may responds with:

    HTTP/1.0 200 OK
    Date: Fri, 15-Jan-2016 07:15:23 GMT
    MIME-version: 1.0
    Content-type: text/html

    <h1>Contact Information</h1>
    <a href="http://sales.ora.com/sales.html">Sales Department</a>

The user clicks on the hyperlink, then sends
[sales.oral.com](sales.oral.com) the following

    GET /sales.html HTTP/1.0
    Accept: */*
    Referer: http://www.ora.com/contact.html

Client and server identification
--------------------------------

Clients identify themselves with `User-Agent` header.

    User-Agent: Mozilla/1.1N (Macintosh; I; 68K)
    User-Agent: HTML-checker/1.0

Servers identify themselves with the `Server` header.

Authorization
-------------

An `Authorization` header is used to request restricted documents.
First, client requests document without `Authentication`, then denied
by server, with `WWW-Authentication` header describing the
authorization method. Then client requests the document again, but with
`Authentication` header.

For example, the client may send

    GET /sample.html HTTP/1.0
    User-Agent: Mozilla/1.1N (Macintosh; I; 68K)
    Accept: */*

The server then responds

    HTTP/1.0 401 Unauthorized
    Date: Sat, 15-Jan-2016 07:29:38 GMT
    Server: NCSA/1.3
    MIME-version: 1.0
    Content-type: text/html
    WWW-Authenticate: BASIC realm=``System Administrator''

The client now seeks authentication information, by prompting to the
user or searching on the hard drive. After encoding the data
appropriately for the BASIC authorization method, the client responds
with proper authentication

    GET /sample.html HTTP/1.0
    User-Agent: Mozilla/1.1N (Macintosh; I; 68K)
    Accept: */*
    Authorization: BASIC d2VibWFzdGVyOnpycW1hNHY=

The server checks the authentication, and sends the requested data.

Cookies
-------

When a server identifies a new user, it adds the `Set-Cookie` header
to its response. The client is expected to save that in disk. In
subsequent requests to that URL, the client should include the cookie
information using the `Cookie` header.

The Socket Library
==================

A typical conversation over sockets
-----------------------------------

The basic idea behind sockets, is that the server sits and waits for
connections over the network to a specific port. When a client connects
to that port, the server accepts and converses with the client, using
whatever protocols like HTTP, NNTP, SMTP.

Initially, the server uses the `socket()` system call to create the
socket, then `bind()` the socket to a particular port on the host. The
server then `listen()` to the port and `accept()` incoming connect
from that port.

On the other hand, the client also create `socket()`, then
`connect()` to the remote socket on a specific host and port. After
the server `accept()` the connection, they can both use `sysread()`
and `syswrite()` to speak to each other. Finally, either the client or
server `close()` or `shutdown()` the connection.

Using the socket
----------------

The socket library is part of the standard Perl distribution. Include
the socket module like this:

```perl
use Socket;
```

To create a socket,

```perl
socket(SH, PF_INET, SOCK_STREAM, getprotobyname('tcp')) || die $!;
```

where `SH` is the file handler associated with the newly created
socket, `PF_INET` indicates the Internet Protocol while
`getprotobyname(’tcp’)` indicates that TCP runs on top of IP.
`SOCK_STREAM` indicates that the socket is stream-oriented.

If the socket call fails, the program should `die()` using the error
message found in `$!`.

Connect the server by

```perl
my $sin = sockaddr_in(80,inet_aton(’www.bing.com’));
connect(SH,$sin) || die $!;
```

where `sockaddr_in()` accepts a port number and a 32-bit IP address,
and return a data structure containing information for an address used
by `connect()`. The `inet_iton(’www.bing.com’)` looks up the IP
address of the given hostname by DNS.

Next, write to the socket by

```perl
$buffer = "Hello world!";
syswrite(SH, $buffer, length($buffer));
```

An easier way is to use `print`.

```perl
select(SH);
$|=1;   # set $| to non-zero to make selection autoflushed
print SH "Hello world!";
```

Then we read the data from socket.

```perl
sysread(SH, $buffer, 200); # read at most 200 bytes from SH and save to $buffer
```

To read a line at a time from the file handle,

```perl
$buffer = <SH> # Read a line from $buffer
```

Finally, close the socket by

```perl
close(SH);
```

The complete client connection code
-----------------------------------

We define a function `open_TCP` in a file `tcp.pl`.

```perl
#############
## open_TCP #
#############
#
# Given ($file_handle, $dest, $port) return 1 if successful, undef when
# unsuccessful.
#
# Input: $fileHandle is the name of the filehandle to use
# $dest is the name of the destination computer,
# either IP address or hostname
# $port is the port number
#
# Output: successful network connection in file handle
#

use Socket;

sub open_TCP
{
  # get parameters
  my ($FS, $dest, $port) = @_;
  my $proto = getprotobyname('tcp');
  socket($FS, PF_INET, SOCK_STREAM, $proto);
  my $sin = sockaddr_in($port,inet_aton($dest));
  connect($FS,$sin) || return undef;
  my $old_fh = select($FS);
  $| = 1;
  select($old_fh);
  1;
}
1;
```

Then we use this function by

```perl
#!/usr/bin/perl

use Socket;
require "tcp.pl";

# If no parameters were given, print out help text
if ($#ARGV) {
  print "Usage: $0 IP_address\n";
  print "\n Returns the HTTP result code from a server.\n\n";
  exit(-1);
}

if(open_TCP(F,$ARGV[0],80) == undef)
{
  print "Error connecting to server at $ARGV[0]\n";
  exit(-1);
}

# Try to get the index webpage.
print F "GET / HTTP/1.1\n\n";

$ReturnStatus=<F>;
print "The server had a response line of: $ReturnStatus\n";

close(F);
```

Parsing a URL
-------------

Let’s start by defining a function for parsing URL.

```perl
#!/usr/bin/perl

# Given a full URL, return the scheme, hostname, port, and path
# into ($scheme, $hostname, $port, $path). We'll only deal with
# HTTP URLs.

sub parse_URL {
  # Get parameter URL
  my ($URL) = @_;

  # attempt to parse
  (my @parsed =$URL =~ m@(\w+)://([^/:]+)(:\d*)?([^#]*)@) || return undef;

  # remove colon from port number
  if(defined $parsed[2]){
	$parsed[2]=~ s/^://;
  }

  # If scheme contains 'http' (ignore case) and path is empty
  # set the path to '/'
  $parsed[3]='/' if ($parsed[0]=~/http/i && (length $parsed[3])==0);

  # if port number is specified, we are done
  return @parsed if (defined $parsed[2]);

  # otherwise, assume port 80, and then we're done
  $parsed[2] = 80;

  @parsed;
}

# grab_urls ($html_content, %tags) returns an array of links that are
# referenced from within html
sub grab_urls {
  # Get parameters
  my($data,%tags) = @_;
  my @urls;

  # while there are HTML tags
  skip_others: while($data =~ s/<([^>]*)>//) {
	my $in_brackets=$1;
	my $key;

	foreach $key (keys %tags) {
	  # if tag matches
	  if ($in_brackets =~ /^\s*$key\s+/i) {
		# if parameter matches
		if ($in_brackets =~ /\s+$tags{$key}\s*=\s*"([^"]*)"/i) {
		  my $link=$1;
		  $link =~ s/[\n\r]//g; # kill new lines
		  push (@urls, $link);
		  next skip_others;
		}
		# if url isn't in quotes
		elsif ($in_brackets =~ /\s+$tags{$key}\s*=\s*([^\s]+)/i) {
		  my $link=$1;
		  $link =~ s/[\n\r]//g; # kill new lines
		  push (@urls, $link);
		  next skip_others;
		}
	  } # If tag matches
	} # foreach tag
  } # while there are brackets
  @urls;
}
1;
```

We can use it to implement a hypertext version of the UNIX `cat`
command.

```perl
#!/usr/bin/perl

# Socket based hypertext version of UNIX cat

use strict;
use Socket;
require "tcp.pl";
require "web.pl";
use vars qw($opt_h $opt_H $opt_r $opt_d);
use Getopt::Std;

# Parse command line arguments
getopts('hHrd');

# Print out usage if -h is set or #argv < 0
if($opt_h != undef|| $#ARGV<0) {help ();}

# For the other arguments, treat them as URL
# Get and print them one by one
while($_ = shift @ARGV)
{
	hcat($_, $opt_r, $opt_H, $opt_d);
}

# Subroutine to print out usage message
sub usage
{
	print "usage: $0 -rhHd URL(s)\n";
	print "       -h           help\n";
	print "       -r           print out response\n";
	print "       -H           print out header\n";
	print "       -d           print out data\n\n";
	exit(-1);
}


# Subroutine to print out help text along with usage information
sub help
{
	print "Hypertext cat help\n\n";
	print "This program prints out documents on a remote web server.\n";
	print "By default, the response code, header, and data are printed\n";
	print "but can be selectively printed with the -r, -H, and -d options.\n\n";

	usage();
}

# Given a URL, print out the data
sub hcat
{
	# Grab parameters
	my ($full_url, $response, $header, $data)=@_;

	# Assume that response, header and data will be printed
	my $all = !($response || $header || $data);

	# If the URL is not a full URL, assume that it is a http request
	$full_url="http://$full_url" if ($full_url !~ m/(\w+):\/\/([^\/:]+)(:\d*)?([^#]*)/);
	# Parse the URL
	my @the_url = &parse_URL($full_url);
	if(@the_url == undef)
	{
		print "Please use fully qualified valid URL\n";
		exit(-1);
	}

	# We are only interested in HTTP URL's
	return if ($the_url[0] !~ m/http/i);

	# Connect to the server specified in 1st parameter
	if(open_TCP('F',$the_url[1],$the_url[2]) == undef)
	{
	  print "Error connecting to server at $the_url[1]\n";
	  exit(-1);
	}

	# Try to get the index webpage.
	print F "GET $the_url[3] HTTP/1.1\n";
	print F "Accept: */*\n";
	print F "User-Agent: hcat/1.0\n";
	print F "Host: $the_url[1]\n\n";

	# Get the HTTP response line
	my $the_response=<F>;
	print $the_response if ($all || $response != undef);

	# get the header data
	while(<F>=~ m/^(\S+):\s+(.+)/)
	{
		print "$1: $2\n" if ($all || $header != undef);
	}

	# get the entity body
	if($all || $data != undef)
	{
		print while (<F>);
	}

	# close the network connection
	close(F);
}
```

We didn’t use the second function `grab_urls()` in `hcat.pl`. This
function takes two arguments, the first is the document content, and the
second is a hash mapping tags to the desired parameters, for example,
`(’img’,’src’,’body’,’background’)`, since the `src` parameter of an
`image` tag and the `background` parameter of a `body` tag are
both URLs. We can use this function to implement a HTTP version of UNIX
`grep` command, `hgrepurl`.

```perl
#!/usr/bin/perl

# Socket based hypertext grep URLs. Given a URL, this
# prints out URLs of hyperlinks and images.

use strict;
use Socket;
require "tcp.pl";
require "web.pl";
use vars qw($opt_h $opt_i $opt_l);
use Getopt::Std;

# Parse command line arguments
getopts('hil');

# Print out usage if -h is set or #argv < 0
if($opt_h != undef|| $#ARGV<0) {help ();}

# For the other arguments, treat them as URL
# Get and print them one by one
while($_ = shift @ARGV)
{
	hgu($_, $opt_i, $opt_l);
}

# Subroutine to print out usage message
sub usage
{
	print "usage: $0 -hil URL(s)\n";
	print "       -h           help\n";
	print "       -i           print out image URLs\n";
	print "       -l           print out hyperlink URLs\n\n";
	exit(-1);
}


# Subroutine to print out help text along with usage information
sub help
{
	print "Hypertext grep URL help\n\n";
	print "This program prints out hyperlink and image links that.\n";
	print "are referenced by a user supplied URL on a web server.\n\n";

	usage();
}

# hypertex grep URL
sub hgu
{
	# Grab parameters
	my ($full_url, $images, $hyperlinks)=@_;
	my $all = !($images || $hyperlinks);
	my @links;
	my @links2;

	# If the URL is not a full URL, assume that it is a http request
	$full_url="http://$full_url" if ($full_url !~ m/(\w+):\/\/([^\/:]+)(:\d*)?([^#]*)/);
	# Parse the URL
	my @the_url = &parse_URL($full_url);
	if(@the_url == undef)
	{
		print "Please use fully qualified valid URL\n";
		exit(-1);
	}

	# We are only interested in HTTP URL's
	return if ($the_url[0] !~ m/http/i);

	# Connect to the server specified in 1st parameter
	if(open_TCP('F',$the_url[1],$the_url[2]) == undef)
	{
	  print "Error connecting to server at $the_url[1]\n";
	  exit(-1);
	}

	# Try to get the index webpage.
	print F "GET $the_url[3] HTTP/1.1\n";
	print F "Accept: */*\n";
	print F "User-Agent: hcat/1.0\n";
	print F "Host: $the_url[1]\n";
	print F "Connection: Close\n\n";

	# Get the HTTP response line
	my $the_response=<F>;

	# if not an "OK" response of 200, skip it
	if($the_response !~ m@^HTTP/\d+\.\d+\s+200\s@) {return ;}

	# get the header data
	while(<F>=~ m/^(\S+):\s+(.+)/) {
		# Skip the headers
	}

	my $data = '';
	# get the entity body
	while (<F>) {
		$data .= $_;
	}

	# close the network connection
	close(F);

	if($images != undef || $all)
	{
		@links = grab_urls($data,('img','src','body','background'));
	}
	if($hyperlinks != undef || $all)
	{
		@links2 = grab_urls($data,('a','href'));
	}

	my $link;
	for $link (@links,@links2) {print "$link\n";}
}
```

The LWP Library
===============

The LWP library is a module for WWW access in Perl, which encapsulate
common functions for a web client or server. LWP is much faster and
cleaner than using sockets.

Some simple examples
--------------------

Simply get a document specified by a URL.

```perl
#!/usr/bin/perl
use LWP::Simple;

print (get $ARGV[0]); # $ARGV[0] is the first command line argument
```

It can even be simpler!

```perl
#!/usr/bin/perl
use LWP::Simple;

getprint($ARGV[0]); # $ARGV[0] is the first command line argument
```

You can use the HTML library to parse the HTML.

```perl
#!/usr/bin/perl
use LWP::Simple;
use HTTP::Parse;

print parse_html(get ($ARGV[0]))->format; # $ARGV[0] is the first command line argument
```

LWP can extract hyperlinks referenced inside an HTML page.

```perl
#!/usr/bin/perl

use LWP::Simple;
use HTML::Parse;
use HTML::Element;
use URI::URL;

$html = get $ARGV[0];
$parsed_html = HTML::Parse::parse_html($html);

for (@{ $parsed_html -> extract_links() }) {
	$link = $_->[0];
	$url = new URI::URL $link;
	$full_url = $url->abs($ARGV[0]);
	print "$full_url\n";
}
```

LWP modules
-----------

There are eight main modules in LWP: File, Font, HTML, HTTP, LWP, MIME,
URI and WWW.

-   **File**: parses directory listings.

-   **Font**: handles Adobe Font Metrics.

-   **HTML**: handles HTML syntax trees.

-   **HTTP**: describes client requests, server responses, and dates,
    and computes a client/server negotiation.

-   **LWP**: the core of all web client programs.

-   **MIME**: converts to/from base 64 and quoted printable text.

-   **URI**: escape URI, specify or translate relative URLs to absolute
    URLs.

-   **WWW**: determine if a server’s resource is accessible via the
    Robot Exclusion Standard.

### The LWP module

There are 10 classes in all within the LWP module: Debug, IO,
MediaTypes, MemberMixin, Protocol, RobotUA, Simple, Socket, TKIO,
UserAgent.

We are mainly interested in Simple, UserAgent, and RobotUA classes.

-   **Simple**: quickly design a web client without complex behaviors.

    -   `get($url)#: returns the content of the URL specified by #$url`.

    -   `head($url)#: returns the header information of the URL specified by #$url`.

    -   `getprint($url)#: print the content of the URL specified by #$url`.

    -   `getstore($url,$file)`: store the content of the URL specified
        by `$url# to file specified by #$file`.

    -   `mirror($url,$file)`: store the content of the URL specified
        by `$url# to file specified by #$file`, only if modified.

    -   `is_success($rc)#: Given a status code from #getprint()#, #getstore()#, or #mirror()#, returns true if the request was successful.
              \item #is_error($rc)`: Given a status code from
        `getprint()`, `getstore()`, or `mirror()`, returns true if
        the request was not successful.

-   **UserAgent**: Create an agent using `new LWP::UserAgent` and
    `request()` a server, return the result of the query.

-   **RobotUA**: A subclass of `LWP::UserAgent`, requesting resources
    in an automated fashion.

### The HTTP module

The HTTP module specifies HTTP requests and responses, plus some helper
functions to interpret or convert data related to HTTP requests and
responses. There are eight classes in HTTP module: Daemon, Date,
Headers, Message, Negotiate, Request, Response, Status.

### The HTML module

The HTML module provides an interface to parse HTML into an HTML parse
tree, traverse the tree, and convert HTML to other formats. There are
eleven classes in the HTML module: AsSubs, Element, Entities, FormatPS,
FormatText, Formatter, HeadParser, LinkExtor, Parse, Parser,
TreeBuilder.

### The URI module

The URI module contains functions and modules to specify and convert
URIs. There are only two classes within the URI module: AsSubs, Element.

Using LWP
---------

First, let’s revisit the simple `getprint` script and make it robust,
using the `LWP::UserAgent` module instead of `LWP::Simple`. We save
it in file `hcat_plain.pl`.

```perl
#!/usr/bin/perl

use LWP::UserAgent;
use HTTP::Request;
use HTTP::Response;

my $ua = new LWP::UserAgent;

my $request = new HTTP::Request('GET',$ARGV[0]);
my $response = $ua->request($request);
if($response->is_success)
{
	print $response->content;
}
else
{
	print $response->error_as_html;
}
```

If it fails to get the web page, an error
page will be printed. It could even follow redirections automatically.

If we use `LWP::RobotUA` instead,

```perl
#!/usr/bin/perl

use LWP::RobotUA;
use HTTP::Request;
use HTTP::Response;

my $ua = new LWP::RobotUA('hcat_RobotUA','example@ora.com');

my $request = new HTTP::Request('GET',$ARGV[0]);
my $response = $ua->request($request);
if($response->is_success)
{
	print $response->content;
}
else
{
	print $response->error_as_html;
}
```

LWP Examples
============

```perl
#!/usr/local/bin/perl5 -w

use strict;
use HTTP::Status;
use HTTP::Response;
use LWP::UserAgent;
use URI::URL;
use vars qw($opt_h $opt_r $opt_H $opt_d $opt_p);
use Getopt::Std;

my $url;
my $goterr;

getopts('hrHdp:');
my $all = !($opt_r || $opt_H || $opt_d);       # all=1 when -r -H -d not set

if ($opt_h || $#ARGV==-1) {  # print help text when -h or no args
  print_help();
  exit(0);
}


my $goterr = 0;  # make sure we clear the error flag

while ($url = shift @ARGV) {

  my ($code, $desc, $headers, $body)=simple_get('GET', $url, $opt_p);
  if ($opt_r || $all) { print "HTTP/1.0 $code $desc\n"; }
  if ($opt_H || $all) { print "$headers\n";             }
  if ($opt_d || $all) { print $body;                    }

  $goterr |= HTTP::Status::is_error($code);
}

exit($goterr);


sub print_help {
  print <<"HELP";
usage: $0 [-hrmbp] [proxy URL] URLs

 -h help
 -r response line only
 -H HTTP header data only
 -d data from entity body
 -p use this proxy server

Example:  hcat -p http://proxy:8080/ http://www.ora.com

HELP
}


sub simple_get() {

  my ($method, $path, $proxy) = @_;

# Create a User Agent object
  my $ua = new LWP::UserAgent;
  $ua->agent("hcat/1.0");

# If proxy server specified, define it in the User Agent object
  if (defined $proxy) {
    my $url = new URI::URL $path;
    my $scheme = $url->scheme;
    $ua->proxy($scheme, $proxy);
  }

# Ask the User Agent object to request a URL.
# Results go into the response object (HTTP::Reponse).

  my $request = new HTTP::Request($method, $path);
  my $response = $ua->request($request);

# Parse/convert the response object for "easier reading"

  my $code=$response->code;
  my $desc = HTTP::Status::status_message($code);
  my $headers=$response->headers_as_string;

  my $body =  $response->content;
  $body =  $response->error_as_HTML if ($response->is_error);

  return ($code, $desc, $headers, $body);
}

```
```perl
#!/usr/local/bin/perl5 -w

use strict;
use HTTP::Status;
use HTTP::Response;
use LWP::UserAgent;
use URI::URL;
use HTML::Parse;
use vars qw($opt_h $opt_i $opt_l $opt_p);
use Getopt::Std;

my $url;

getopts('hilp:');
my $all = !($opt_i || $opt_l);       # $all=1 when -i -l not set

if ($opt_h || $#ARGV==-1) {  # print help text when -h or no args
  print_help();
  exit(0);
}


while ($url = shift @ARGV) {

  my ($code, $type, $data)  = get_html($url, $opt_p, $opt_i, $opt_l);
  if (not_good($code, $type)) { next; }
  if ($opt_i || $all) { print_images($data, $url); }
  if ($opt_l || $all) { print_hyperlinks($data, $url); }

} # while there are URLs on the command line

sub print_help {
  print << "HELP";
usage: $0 [-hilp] [proxy URL] URLs

 -h help
 -i grep out images references only
 -l grep out hyperlink references only
 -p use this proxy server

Example:  $0 -p http://proxy:8080/ http://www.ora.com

HELP
}

sub get_html() {
  my($url, $proxy, $want_image, $want_link) = @_;

# Create a User Agent object
  my $ua = new LWP::UserAgent;
  $ua->agent("hgrepurl/1.0");

# If proxy server specified, define it in the User Agent object
  if (defined $proxy) {
    my $proxy_url = new URI::URL $url;
    $ua->proxy($proxy_url->scheme, $proxy);
  }

# Ask the User Agent object to request a URL.
# Results go into the response object (HTTP::Reponse).

  my $request = new HTTP::Request('GET', $url);
  my $response = $ua->request($request);

  return ($response->code, $response->content_type, $response->content);
}



# returns 1 if the request was not OK or HTML, else 0

sub not_good {
  my ($code, $type) = @_;

  if ($code != RC_OK) {
    warn("$url had response code of $code");
    return 1;
  }

  if ($type !~ m@text/html@) {
    warn("$url is not HTML.");
    return 1;
  }
  return 0;
}

sub print_images {

  my ($data, $model) = @_;

  my $parsed_html=HTML::Parse::parse_html($data);
  for (@{ $parsed_html->extract_links(qw (body img)) }) {
    my ($link) = @$_;
    my ($absolute_link) = globalize_url($link, $model);
    print "$absolute_link\n";
  }
  $parsed_html->delete(); # manually do garbage collection
}

sub print_hyperlinks {

  my ($data, $model) = @_;

  my $parsed_html=HTML::Parse::parse_html($data);
  for (@{ $parsed_html->extract_links(qw (a)) }) {
    my ($link) = @$_;
    my ($absolute_link) = globalize_url($link, $model);
    print "$absolute_link\n";
  }
  $parsed_html->delete(); # manually do garbage collection
}


sub globalize_url() {

  my ($partial, $model) = @_;
  my $url = new URI::URL($partial, $model);
  my $globalized = $url->abs->as_string;

  return $globalized;
}


```

```perl
#!/usr/local/bin/perl5 -w 
use strict;

use HTML::FormatText;
use HTML::Parse;
use vars qw($opt_h $opt_a $opt_e $opt_d $opt_c $opt_p);
use Getopt::Std;

# URL that handles our FedEx query
my $cgi = 'http://www.fedex.com/cgi-bin/track_it';

getopts('ha:e:d:c:p:');

# print help upon request or when arguments are missing
if ($opt_h  || !($opt_a && $opt_e && $opt_d && $opt_c )) {
  print_help();
  exit(0);
}

# 
my $tracker = new FedEx $cgi, $opt_e, $opt_p;

my $keep_checking = 1;

while ($keep_checking) {
  $tracker->check($opt_a, $opt_c, $opt_d);

  if ($tracker->retrieve_okay) {

    if ($tracker->delivered) {
      print "Tracking number $opt_a was delivered to: ",
             $tracker->who_got_it, "\n";
      $keep_checking = 0;

    } 
    else {

      # request was okay, but not delivered.  Let's wait
      sleep (60 * 30);  # sleep 30 minutes
    }

  } 
  else {

    # request not successful
    my $html_error_message = $tracker->error_info;

    my $parsed    = parse_html($html_error_message);
    my $converter = new HTML::FormatText;
    print $converter->format($parsed);
    
    $keep_checking = 0;
  }
}


sub  print_help {

  print <<HELP
This program prints a notification when a FedEx shipment is delivered.
fedex -a 1234 -e user\@host.com -d 120396 -c U.S.A. [ -p http://host:port/ ] 

h - this help text
a - airbill number
e - your email address
d - date in MMDDYY format that document was sent
c - country of recipient
p - use this proxy server [optional]
HELP
}

package FedEx;

use HTTP::Request;
use HTTP::Response;
use LWP::RobotUA;
use HTTP::Status;

sub new {

  my($class, $cgi_url, $email, $proxy) = @_;
  my $user_agent_name = 'ORA-Check-FedEx/1.0';

  my $self  = {};
  bless $self, $class;

  $self->{'url'} = new URI::URL $cgi_url;

  $self->{'robot'} = new LWP::RobotUA $user_agent_name, $email; 
  $self->{'robot'}->delay(0);    # we'll delay requests by hand

  if ($proxy) {
    $self->{'robot'}->proxy('http', $proxy);
  }

  $self;
}

sub check {

  my ($self, $track_num, $country, $date) = @_;

  $self->{'url'}->query("trk_num=$track_num&dest_cntry=" .
    "$country&ship_date=$date");
  my $request = new HTTP::Request 'GET', $self->{'url'};

  my $response = $self->{'robot'}->request($request);
  $self->{'status'} = $response->code();

  if ($response->code == RC_OK) {

    if ($response->content =~ /Delivered To : (\w.*)/) {

      # package delivered
      $self->{'who_got_it'} = $1;
      $self->{'delivered'} = 1;
    } 

    # Odd cases when package is delivered but "Delivered To" is blank.
    # Check for delivery time instead.

    elsif ($response->content =~ /Delivery Time : \w.*/) {

      # package delivered
      $self->{'who_got_it'} = 'left blank by FedEx computer';
      $self->{'delivered'} = 1;
    }
    else {

      # package wasn't delivered
      $self->{'delivered'} = 0;

      # if there isn't a "Delivered To : " field, something's wrong.
      # error messages seen between HTML comments

      if ($response->content !~ /Delivered To : /) {
        $self->{'status'} = RC_BAD_REQUEST;

        # get explanation from HTML response
	my $START = '<!-- BEGIN TRACKING INFORMATION -->';
	my $END = '<!-- END TRACKING INFORMATION -->';

        if   ($response->content =~ /$START(.*?)$END/s) {
    
	  $self->{'error_as_HTML'} = $1;
        
        } 
        else {
          # couldn't get explanation, use generic one
          $self->{'error_as_HTML'} = 'Unexpected HTML response from FedEx'; 

        }    # couldn't get error explanation
      }      # unexpected reply
    }        # not delivered yet
  }          # if HTTP response of RC_OK (200)
  else {
    $self->{'error_as_HTML'} = $response->error_as_HTML;
  }

}

sub retrieve_okay {

  my $self = shift;
  if ($self->{'status'} != RC_OK) {return 0;}
  1;
}

sub delivered {

  my $self = shift;
  $self->{'delivered'};
}

sub who_got_it {

  my $self = shift;
  $self->{'who_got_it'};
}

sub error_info {

  my $self = shift;
  $self->{'error_as_HTML'};
}

```

```perl
#!/usr/local/bin/perl5 -w
use strict;

use vars qw($opt_a $opt_v $opt_l $opt_r $opt_R $opt_n $opt_b
            $opt_h $opt_m $opt_p $opt_e $opt_d);
use Getopt::Std;


# Important variables
#----------------------------
# @lookat     queue of URLs to look at
# %local      $local{$URL}=1  (local URLs in associative array)
# %remote     $remote{$URL}=1 (remote URLs in associative array)
# %ref        $ref{$URL}="URL\nURL\n" (list of URLs separated by \n)
# %touched    $touched{$URL}=1 (URLs that have been visited)
# %notweb     $notweb{$URL}=1 if URL is non-HTTP
# %badlist    $badlist{$URL}="reason" (URLs that failed. Separated with \n)

getopts('avlrRnbhm:p:e:d:');

# Display help upon -q, no args, or no e-mail address

if ($opt_h || $#ARGV == -1 || (! $opt_e) ) {
  print_help();
  exit(-1);
}

# set maximum number of URLs to visit to be unlimited

my ($print_local, $print_remote, $print_ref, $print_not_web,
    $print_bad,   $verbose,      $max,       $proxy,
    $email,       $delay,        $url);

$max=0;

if ($opt_l) {$print_local=1;}
if ($opt_r) {$print_remote=1;}
if ($opt_R) {$print_ref=1;}
if ($opt_n) {$print_not_web=1;}
if ($opt_b) {$print_bad=1;}
if ($opt_v) {$verbose=1;}
if (defined $opt_m) {$max=$opt_m;}
if ($opt_p) {$proxy=$opt_p;}
if ($opt_e) {$email=$opt_e;}
if (defined $opt_d) {$delay=$opt_d;}
if ($opt_a) {
  $print_local=$print_remote=$print_ref=$print_not_web=$print_bad = 1;
}

my $root_url=shift @ARGV;

# if there's no URL to start with, tell the user
unless ($root_url) {
  print "Error: need URL to start with\n";
  exit(-1);
}

# if no "output" options are selected, make "print_bad" the default
if (!($print_local || $print_remote || $print_ref ||
  $print_not_web || $print_bad)) {
  $print_bad=1;
}

# create CheckSite object and tell it to scan the site
my $site = new CheckSite($email, $delay, $max, $verbose, $proxy);
$site->scan($root_url);

# done with checking URLs.  Report results


# print out references to local machine
if ($print_local) {
  my %local = $site->local;

  print "\nList of referenced local URLs:\n";
  foreach $url (keys %local) {
    print "local: $url\n";
  }
}


# print out references to remote machines
if ($print_remote) {
  my %remote = $site->remote;

  print "\nList of referenced remote URLs:\n";
  foreach $url (keys %remote) {
    print "remote: $url\n";
  }
}


# print non-HTTP references
if ($print_not_web) {
  my %notweb = $site->not_web;

  print "\nReferenced non-HTTP links:\n";
  foreach $url (keys %notweb) {
    print "notweb: $url\n";
  }
}

# print reference list (what URL points to what)
if ($print_ref) {
  my $refer_by;
  my %ref = $site->ref;

  print "\nReference information:\n";
  while (($url,$refer_by) = each %ref) {
    print "\nref: $url is referenced by:\n";
    $refer_by =~ s/\n/\n  /g;  # insert two spaces after each \n
    print "  $refer_by";
  }
}

# print out bad URLs, the server response line, and the Referer
if ($print_bad) {
  my $reason;
  my $refer_by;
  my %bad = $site->bad;
  my %ref = $site->ref;

  print "\nThe following links are bad:\n";
  while (($url,$reason) = each %bad) {
    print "\nbad: $url  Reason: $reason";
    print "Referenced by:\n";
     $refer_by = $ref{$url};
     $refer_by =~ s/\n/\n  /g;  # insert two spaces after each \n
     print "  $refer_by";
  } # while there's a bad link
} # if bad links are to be reported


sub print_help() {
  print <<"USAGETEXT";
Usage:  $0 URL\n
Options:
  -l         Display local URLs
  -r         Display remote URLs
  -R         Display which HTML pages refers to what
  -n         Display non-HTML links
  -b         Display bad URLs (default)
  -a         Display all of the above
  -v         Print out URLs when they are examined
  -e email   Mandatory: Specify email address to include
	     in HTTP request. 
  -m #       Examine at most # URLs\n
  -p url     Use this proxy server
  -d #       Delay # minutes between requests.  (default=1)
	     Warning: setting # to 0 is not very nice.
  -h         This help text

Example: $0 -e me\@host.com -p http://proxy/ http://site_to_check/
USAGETEXT
  }



package CheckSite;

use HTTP::Status;
use HTTP::Request;
use HTTP::Response;
use LWP::RobotUA;
use URI::URL;

sub new {

  my ($class, $email, $delay, $max, $verbose, $proxy) = @_;
  my $self = {};
  bless $self, $class;


  # Create a User Agent object, give it a name, set delay between requests
  $self->{'ua'} = new LWP::RobotUA 'ORA_checksite/1.0', $email;
  if (defined $delay) {$self->{'ua'}->delay($delay);}

  # If proxy server specified, define it in the User Agent object
  if (defined $proxy) {
    $self->{'ua'}->proxy('http', $proxy);
  }

  $self->{'max'} = $max;
  $self->{'verbose'} = $verbose;

  $self;
}

sub scan {

  my ($self, $root_url)   = @_;
  my $verbose_link;
  my $num_visited = 0;
  my @urls;

  # clear out variables from any previous call to scan()
  undef %{ $self->{'bad'} };
  undef %{ $self->{'not_web'} };
  undef %{ $self->{'local'} };
  undef %{ $self->{'remote'} };
  undef %{ $self->{'type'} };
  undef %{ $self->{'ref'} };
  undef %{ $self->{'touched'} };

  my $url_strict_state = URI::URL::strict();   # to restore state later
  URI::URL::strict(1);


  my $parsed_root_url = eval { new URI::URL $root_url; };
  push (@urls , $root_url);
  $self->{'ref'}{$root_url} = "Root URL\n";


  while (@urls) {            # while URL queue not empty
    my $url=shift @urls;      # pop URL from queue & parse it

    # increment number of URLs visited and check if maximum is reached
    $num_visited++;
    last if (  ($self->{'max'}) && ($num_visited > $self->{'max'}) );

    # handle verbose information
    print STDERR "Looking at $url\n" if ($self->{'verbose'});

    my $parsed_url = eval { new URI::URL $url; };

    # if malformed URL (error in eval) , skip it
    if ($@) {
      $self->add_bad($url, "parse error: $@");
      next;
    }

    # if not HTTP, skip it
    if ($parsed_url->scheme !~ /http/i) {
      $self->{'not_web'}{$url}=1;
      next;
    }

    # skip urls that are not on same server as root url
    if (same_server($parsed_url, $parsed_root_url)) { 
      $self->{'local'}{$url}=1;
    } else {                                     # remote site
      $self->{'remote'}{$url}=1;
      next;              # only interested in local references
    }

    # Ask the User Agent object to get headers for the url
    # Results go into the response object (HTTP::Response).


    my $request  = new HTTP::Request('HEAD', $url);
    my $response = $self->{'ua'}->request($request);

    # if response wasn't RC_OK (200), skip it
    if ($response->code != RC_OK) {
      my $desc = status_message($response->code);
      $self->add_bad($url, "${desc}\n");
      next;
    }

    # keep track of every url's content-type
    $self->{'type'}{$url} = $response->header('Content-Type');

    # if not HTML, don't bother to search it for URLs
    next if ($response->header('Content-Type') !~ m@text/html@ );

    
    # it is text/html, get the entity-body this time
    $request->method('GET');
    $response = $self->{'ua'}->request($request);

    # if not OK or text/html... weird, it was a second ago.  skip it.
    next if ($response->code != RC_OK);
    next if ($response->header('Content-Type') !~ m@text/html@ );

    my $data     = $response->content;
    my @rel_urls = grab_urls($data);

    foreach $verbose_link (@rel_urls) {

      my $full_child =  eval {
        (new URI::URL $verbose_link, $response->base)->abs($response->base,1);
      };
      
      # if LWP doesn't recognize the child url, treat it as malformed
      if ($@) {

	# update list of bad urls, remember where it happened
	$self->add_bad($verbose_link, "unrecognized format: $@");
        $self->add_ref($verbose_link, $url);

        next;
      }
      else {

        # remove fragment in http urls
        if ( ($full_child->scheme() =~ /http/i) ) {
          $full_child->frag('');
        }

        # handle reference list and push unvisited links onto queue
        $self->add_ref($full_child, $url);
        if (! defined $self->{'touched'}{$full_child}) {
          push (@urls, $full_child);
	}

        # remember which url we just pushed, to avoid repushing
        $self->{'touched'}{$full_child} = 1;

      }   # process valid links on page
    }     # foreach url in this page
  }       # while url(s) in queue

  URI::URL::strict($url_strict_state);  # restore state before exiting

} # scan

sub same_server {
  my ($host1, $host2) = @_;

  my $host2_name = $host2->host;

  if ($host1->host !~ /^$host2_name$/i) {return 0;}
  if ($host1->port != $host2->port) {return 0;}

  1;
}

# grab_urls($html_content) returns an array of links that are referenced
# from within the html.  Covers <body background>, <img src> and <a href>.
# This includes more a little more functionality than the 
# HTML::Element::extract_links() method.
#BACK
sub grab_urls {

  my ($data) = @_;
  my @urls;
  my $key;
  my $link;


  my %tags = (
    'body' => 'background', 
    'img'  => 'src',
    'a'    => 'href'
  );

  # while there are HTML tags
  skip_others: while ($data =~ s/<([^>]*)>//)  {

    my $in_brackets=$1;

    foreach $key (keys %tags) {

      if ($in_brackets =~ /^\s*$key\s+/i) {     # if tag matches, try parms
        if ($in_brackets =~ /\s+$tags{$key}\s*=\s*["']([^"']*)["']/i) {
          $link=$1;
          $link =~ s/[\n\r]//g;  # kill newlines,returns anywhere in url
          push @urls, $link;
	  next skip_others;
        } 
	# handle case when url isn't in quotes (ie: <a href=thing>)
        elsif ($in_brackets =~ /\s+$tags{$key}\s*=\s*([^\s]+)/i) {
          $link=$1;
          $link =~ s/[\n\r]//g;  # kill newlines,returns anywhere in url
          push @urls, $link;
	  next skip_others;
        }    
      }       # if tag matches
    }         # foreach <a|img|body>
  }           # while there are brackets
  @urls;
}

# public interface to class's internal variables

# return associative array of bad urls and their error messages
sub bad {
  my $self = shift;
  %{ $self->{'bad'} };
}

# return associative array of encountered urls that are not http based
sub not_web {
  my $self = shift;
  %{ $self->{'not_web'} };
}

# return associative array of encountered urls that are local to the
# web server that was queried in the latest call to scan()

sub local {
  my $self = shift;
  %{ $self->{'local'} };
}

# return associative array of encountered urls that are not local to the
# web server that was queried in the latest call to scan()

sub remote {
  my $self = shift;
  %{ $self->{'remote'} };
}

# return associative array of encountered urls and their content-type
sub type {
  my $self = shift;
  %{ $self->{'type'} };
}

# return associative array of encountered urls and their parent urls,
# where parent urls are separated by newlines in one big string

sub ref {
  my $self = shift;
  %{ $self->{'ref'} };
}

# return associative array of encountered urls.  If we didn't push it
# into the queue of urls to visit, it isn't here.

sub touched {
  my $self = shift;
  %{ $self->{'touched'} };
}

# add_bad($child, $parent)
#   This keeps an associative array of urls, where the associated value 
#   of each url is an error message that was encountered when
#   parsing or accessing the url.  If error messages already exist for
#   the url, any additional error messages are concatenated to existing
#   messages.

sub add_bad {
  my ($self, $url, $msg) = @_;

  if (! defined $self->{'bad'}{$url} ) {
    $self->{'bad'}{$url}  = $msg;
  }
  else {
    $self->{'bad'}{$url} .= $msg;
  }
}

# add_ref($child, $parent)
#   This keeps an associative array of urls, where the associated value
#   of each url is a string of urls that refer to it.  So if 
#   url 'a' and 'b' refer to url 'c', then $self->{'ref'}{'c'}
#   would have a value of 'a\nb\n'.  The newline separates parent urls.

sub add_ref {

  my ($self, $child, $parent) = @_;

  if (! defined  $self->{'ref'}{$child} ) {
    $self->{'ref'}{$child} = "$parent\n";
  } 
  elsif ($self->{'ref'}{$child} !~ /$parent\n/) {
    $self->{'ref'}{$child} .= "$parent\n";
  }

}

```

Graphical Examples with Perl/Tk
===============================

Introduction to Tk
------------------

Tk gives your browser a Graphical User Interface. Here is a simple
“Hello World” program for Tk.

```perl
#!/usr/bin/perl
use Tk;
my $mw = MainWindow->new;
$mw->Button(-text => "Hello World!", -command => sub { exit} )->pack;
MainLoop;
```

When you run this script, a small window
will show up with only a button on it. Press the button and the program
will exit.

Note: the `=&gt;` simple is functionally the same to comma (`,`). It
just makes it easier to detect “pairs” of items.

Examples
--------

The following example is a dictionary program looking up a word on a
website.

```perl
#!/usr/bin/perl

use Tk;
require LWP::UserAgent;
use HTML::Parse;

%html_action =
  (
   "</TITLE>", \&end_title,
   "<H1>", \&start_heading,
   "</H1>", \&end_heading,
   "<H2>", \&start_heading,
   "</H2>", \&end_heading,
   "<H3>", \&start_heading,
   "</H3>", \&end_heading,
   "<H4>", \&start_heading,
   "</H4>", \&end_heading,
   "<H5>", \&start_heading,
   "</H5>", \&end_heading,
   "<H6>", \&start_heading,
   "</H6>", \&end_heading,
   "<P>", \&paragraph,
   "<BR>", \&line_break,
   "<HR>", \&draw_line,
   "<A>", \&flush_text,
   "</A>", \&end_link,
   "</BODY>", \&line_break,
  );

$ua = new LWP::UserAgent;
$dictionary_url = "http://work.ucsd.edu:5141/cgi-bin/http_webster";

$mw = MainWindow->new;
$mw->title("xword");
$mw->CmdLine;

$frame1 = $mw->Frame(-borderwidth => 2,
		     -relief => 'ridge');
$frame1->pack(-side => 'top',
	      -expand => 'n',
	      -fill => "x");
$frame2 = $mw->Frame;
$frame2->pack(-side => 'top', -expand => 'yes', -fill => 'both');
$frame3 = $mw->Frame;
$frame3->pack(-side => 'top', -expand => 'no', -fill => 'x');

$frame1->Label(-text => "Enter Word: ")->pack(-side => "left",
					      -anchor => "w");
$entry = $frame1->Entry(-textvariable => \$word,
			-width => 40);
$entry->pack(-side => "left",
	     -anchor => "w",
	     -fill => "x",
	     -expand => "y");

$bttn = $frame1->Button(-text => "Lookup",
			-command => sub { &do_search(); });
$bttn->pack(-side => "left",
	    -anchor => "w");

$entry->bind('<Return>', sub { &do_search(); } );

$scroll = $frame2->Scrollbar;
$text = $frame2->Text(-yscrollcommand => ['set', $scroll],
		      -wrap => 'word',
		      -font => 'lucidasans-12',
		      -state => 'disabled');
$scroll->configure(-command => ['yview', $text]);
$scroll->pack(-side => 'right', -expand => 'no', -fill => 'y');
$text->pack(-side => 'left', -anchor => 'w',
	    -expand => 'yes', -fill => 'both');

$frame3->Label(-textvariable => \$INFORMATION,
	       -justify => 'left')->pack(-side => 'left',
					 -expand => 'no',
					 -fill => 'x');
$frame3->Button(-text => "Exit",
	    -command => sub{exit} )->pack(-side => 'right',
				       -anchor => 'e');
$text->tag('configure', '</H1>', -font => 'lucidasans-bold-24');
$text->tag('configure', '</H2>', -font => 'lucidasans-bold-18');
$text->tag('configure', '</H3>', -font => 'lucidasans-bold-14');
$text->tag('configure', '</H4>', -font => 'lucidasans-bold-12');
$text->tag('configure', '</H5>', -font => 'lucidasans-bold-12');
$text->tag('configure', '</H6>', -font => 'lucidasans-bold-12');
$entry->focus;
MainLoop;
sub do_search {
    my ($url) = @_;
    
    return if ($word =~ /^\s*$/);
    
    $url = "$dictionary_url?$word" if (! defined $url);

$INFORMATION = "Connect: $url";

    $text->configure(-cursor=> 'watch');
    $mw->idletasks;
    my $request = new HTTP::Request('GET', $url);
    
    my $response = $ua->request($request);
    if ($response->is_error) {
	$INFORMATION = "ERROR: Could not retrieve $url";
    } elsif ($response->is_success) {
	my $html = parse_html($response->content);

	## Clear out text item
	$text->configure(-state => "normal");
	
	$text->delete('1.0', 'end');
	$html->traverse(\&display_html);
	$text->configure(-state => "disabled");
	$html_text = "";
	$INFORMATION = "Done";
    }
    
    $text->configure(-cursor => 'top_left_arrow'); 
}
sub display_html {
    my ($node, $startflag, $depth) = @_;
    my ($tag, $type, $coderef);  ## This tag is the HTML tag...
    
    if (!ref $node) {
$html_text .= $node;
    } else {
if ($startflag) {
$tag = $node->starttag;
} else {
	    	$tag = $node->endtag;
}

## Gets rid of any 'extra' stuff in the tag, and saves it
if ($tag =~ /^(<\w+)\s(.*)>/) {
	    	$tag = "$1>";
	    	$extra = $2;
}
	
if (exists $html_action{$tag}) {
$html_text =~ s/\s+/ /g;
	    	&{ $html_action{$tag} }($tag, $html_text);
	   	$html_text = "";
}
    }
    1;
}

sub end_title {
    $mw->title("xword: ". $_[1]);
}
sub start_heading {
    &flush_text(@_);
    $text->insert('end', "\n\n");
}
sub end_heading {
    $text->insert('end', $_[1], $_[0]);
    $text->insert('end', "\n");
}
sub paragraph {
    &flush_text(@_);
    $text->insert('end', "\n\n");
}
sub line_break {
    &flush_text(@_);
    $text->insert('end', "\n");
}

sub draw_line {
    &flush_text(@_);
    $text->insert('end',
"\n---------------------------------------------\n");
}

sub flush_text {
    $text->insert('end', $_[1]);
}

sub end_link {
    ## Don't want to add links to mailto refs.
    if ($extra =~ /HREF\s*=\s*"(.+)"/ && $extra !~ /mailto/) {
	my $site = $1;

	## The tags must have unique names to allow for a different
	## binding to it. (Otherwise we'd just be changing that same 
	## tag binding over and over again)
	
	my $newtag = "LINK". $cnt++;
	
	$text->tag('configure', $newtag, -underline => 'true',
		   -foreground => 'blue');
	$text->tag('bind', $newtag, '<Enter>', 
		   sub { $text->configure(-cursor => 'hand2');
			 $INFORMATION = $site; });
	$text->tag('bind', $newtag, '<Leave>', 
		   sub { $text->configure(-cursor =>
'top_left_arrow');
			 $INFORMATION = "";});
	
	$text->tag('bind', $newtag, '<ButtonPress>', 
		   sub { &do_search($site); });
	
	$text->insert('end', $_[1], $newtag);
    } else {
	&flush_text(@_);
    }

}

```

The following example is a graphical `httping`.

```perl
#!/usr/bin/perl
#############################################################################
## Webping: A program that will detect and report whether a web site is up.
## usage: webping [ -a ] [ -i <minutes>] [ -f <filename> ] [-- [ -geometry...]]
##   -a : starts prog in "autoping" mode from beginning.
##   -i : Sets the autoping interval to <int>
##   -f : Uses <filename> instead of .webping_sites as site list
##   -- is necessary to separate webping's options from the Window
##      Manager options.  Allows us to utilize GetOptions instead of parsing
##      them manually (ick).
##   The standard wm specs are allowed after the --, -geometry and -iconic
##   being the most useful of them.
#############################################################################

use Tk;
use LWP::Simple;
use Getopt::Long;

## DEFAULT values -- may be changed by specifing cmd line options.
my $site_file = "$ENV{HOME}/.webping_sites";
$ping_interval = 5;
$auto_mode = 0;
@intervals = (5, 10, 15, 20, 25, 30, 35);

sub numerically { $a <=> $b; }
sub antinumerically { $b <=> $a; }
		  
## Parse our specific command line options first
&GetOptions("i=i" => \$ping_interval,
  "f=s" => \$site_file,
	         "a" => \$auto_mode);

if (! grep /$ping_interval/, @intervals) {
    push (@intervals, $ping_interval);
}
my $mw = MainWindow->new;
$mw->title("Web Ping");
$mw->CmdLine;  ## parse -geometry and etc cmd line options.

$frame1 = $mw->Frame;
$frame1->pack(-side => "bottom", -anchor => "n",
	      -expand => "n", -fill => "x");

## Create frame for buttons along the bottom of the window
my $button_f = $frame1->Frame(-borderwidth => 2,
			      -relief => "ridge");
$button_f->pack(-side => "top", -anchor => "n",
		-expand => "n",	-fill => "x");

$update_bttn = $button_f->Button(-text => "Update",
				 -state => 'disabled',
				 -command => sub { &end_automode;
						   &ping_site });
$update_bttn->pack(-side => "left", -anchor => "w", -padx => 5);

$del_bttn = $button_f->Button(-text => "Delete",
			      -state => 'disabled',
			      -command => sub { &delete_site });
$del_bttn->pack(-side => "left",
		-anchor => 'w',
		-padx => 10);

$automode_bttn = $button_f->Button(-text => "Start Automode",
				   -command => \&do_automode);
$automode_bttn->pack(-side => 'left');

$button_f->Label(-text => "Interval: ")->pack(-side => "left");

## Create a psuedo pop-up menu using Menubutton
$interval_mb = $button_f->Menubutton(-indicatoron => 1,
			    -borderwidth => 2,
			    -relief => "raised");
$interval_mb->pack(-side => "left");

$interval_mb->configure(-menu => $interval_mb->Menu(-tearoff => 0),
	       -textvariable => \$ping_interval);
map { $interval_mb->radiobutton(-label => $_,
		       -variable => \$ping_interval,
		       -value => $_,
		       -indicatoron => 0) } sort numerically
@intervals;


$button_f->Button(-text => "Exit",
		  -command => \&exit_program)->pack(-side => "right",
						    -anchor => "e");

my $entry_f = $mw->Frame;
$entry_f->pack(-side => 'top', -anchor => 'n', -fill => 'x');

$entry_f->Label(-text => "URL: ")->pack(-side => 'left',
					-anchor => 'w');
my $entry = $entry_f->Entry(-textvariable => \$url);
$entry->pack(-side => 'left', -anchor => 'w', -expand => 'y', -fill =>
'x');


$entry_f->Button(-text => "Ping",
	       -command => \&add_site)->pack(-side => 'left',
					     -anchor => 'e');
$entry->bind('<Return>', \&add_site);

my $list_f = $mw->Frame;
$list_f->pack(-side => 'top',
	      -anchor => 'n',
	      -expand => 'yes',
	      -fill => 'both');
$history_label = $list_f->Button(-text => "History:",
				 -borderwidth => 2,
				 -relief => "flat");
$history_label->pack(-side => 'top', -anchor => 'n', -fill => 'x');

my $scroll = $list_f->Scrollbar;
my $list = $list_f->Listbox(-selectmode => 'extended',
			    -yscrollcommand => ['set', $scroll]);
$scroll->configure(-command => ['yview', $list]);
$scroll->pack(-side => 'right', -fill => 'y');
$list->pack(-side => 'left', -expand => 'yes', -fill => 'both');

## Bind Listbox so that the "Update" button is enabled whenever a user
## has an item selected.
$list->bind('<Button-1>', sub { 
    my @selected = $list->curselection;
    if ($#selected >= 0) {
	$update_bttn->configure(-state => 'normal');
	$del_bttn->configure(-state => 'normal');
    } else {
	$update_bttn->configure(-state => 'disabled');
	$del_bttn->configure(-state => 'disabled');
    }
} );

if (open(FH, "$site_file")) {
    while (<FH>) {
	chomp;
	$url = $_;
	&add_site;
    }
    close FH;
}
$url = "";

$entry->focus;

&do_automode if ($auto_mode);

MainLoop;

sub exit_program {
    my @updated = $list->get(0, 'end');
    if (open FH, ">$site_file") {
	map { print FH "$_\n"; } @updated;
	close FH;
    }
    exit;
}
sub ping_site {
  ## get list of indexes in listbox of those selected.
  my $site = "";
  my ($content, @down);
  my @selected = $list->curselection;
  
  $mw->configure(-cursor => 'watch'); 
  $mw->idletasks;

  foreach $index (@selected) {
      my $site = $list->get($index);
      $site =~ s/\s.+$//;         ## Strip off last history record (if any)
      
      $content = head($site);
      if ($content) {
	  $site .= " is UP (" . localtime() .")";
      } else {
	  $site .= " is DOWN (" . localtime() .")";
	  push (@down, $site);
      }
      $list->delete($index);
      $list->insert($index, $site);
  }
  
  ## Since we've deleted and inserted into the box -- the sites prev
  ## selected now aren't. Luckily we know which ones those were.
  map { $list->selection('set', $_) } @selected;
  
  ## Set cursor back to the default value
  $mw->configure(-cursor => 'top_left_arrow'); 
  
  if ($#down >= 0) {
      $mw->deiconify;
      $mw->update;
      
      $old_color = $history_label->cget(-background);
      
      ## Do some stuff to make the user pay attention to us.
      $history_label->configure(-background => "red");
      $history_label->bell;
      $history_label->flash;       $history_label->flash;
      $history_label->configure(-background => $old_color);
  }

}
sub add_site {
    return if ($url eq "");                ## Do nothing, empty string
   
    ## Validate $url contains correct information (ie a server name)
    $url = "http://$url" if ($url !~ /(\w+):\/\//);
    
    ## Insert the new site name into the list, and make sure we can see it.
    $list->insert('end', $url);
    $list->see('end');
    
    ## Select the item so that our function ping_site can do all the work
    $list->selection('clear', 0, 'end');
    $list->selection('set', $list->size - 1);  
    
    $url = "";   ## Clear out string for next site

    &ping_site;
}
sub delete_site {
    my @selected = $list->curselection;

    ## Have to delete the items out of list backwards so that the indexes
    ## we just retrieved remain valid until we're done.
    map { $list->delete($_) } sort antinumerically @selected;

    $update_bttn->configure(-state => 'disabled');
    $del_bttn->configure(-state => 'disabled');
}
sub do_automode {
    ## State if the $automode_bttn will tell us which way we are in.
    my $state = $automode_bttn->cget(-text);

    if ($state =~ /^Start/) {
	$automode_bttn->configure(-text => "End Automode");

	$mw->iconify if ($auto_mode);
	
	$interval_mb->configure(-state => 'disabled');
	
	## If the user started up from scratch -- then select all (doesn't
	## make sense to ping _nothing_.
	@selected = $list->curselection;
	$list->selection('set', 0, 'end') if ($#selected < 0);
	$id = $mw->repeat($ping_interval * 60000, \&ping_site);
    } else {
	&end_automode;
    }
}
## end of do_automode
#########################################################
sub end_automode {	
my $state = $automode_bttn->cget(-text);
   $interval_mb->configure(-state => 'normal');
   if ($state =~ /^End/) {
$automode_bttn->configure(-text => "Start Automode");
	   $mw->after('cancel', $id);
   }
}

```
