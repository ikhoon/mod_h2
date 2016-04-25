
#mod_h[ttp]2 - http/2 for Apache httpd

Copyright (C) 2015, 2016 greenbytes GmbH

<<<<<<< HEAD
Copying and distribution of this file, with or without modification,
are permitted in any medium without royalty provided the copyright
notice and this notice are preserved.  This file is offered as-is,
without warranty of any kind. See LICENSE for details.


This repository contains the `mod_h[ttp]2` for Apache httpd. It enables the HTTP2
protocol inside the server, using nghttp2 (https://nghttp2.org) as base engine.

##Status
**Released in Apache httpd 2.4.17**: You find the packages [on the Apache download page](https://httpd.apache.org/download.cgi).

All future development will happen in the Apache repository. I may offer some alpha versions here between releases. 

##Packages
If you want it on your system, there are people who have built packages. For example:

* **Ubuntu**: [ppa by ondrej](https://launchpad.net/~ondrej/+archive/ubuntu/apache2) which I have tested on a 14.04. Not sure what Ubuntu 15.10 all inclues, but at least OpenSSL 1.0.2 is in there.
* **FreeBSD**: [Sp1l](https://github.com/Sp1l) has been working on mod_http2 ports for some time. Not sure what he recommends.
* **Fedora**: [Rawhide has packages](http://rpmfind.net/linux/rpm2html/search.php?query=httpd)

Tell me about other options, so I can list them here!
=======
This repository contains `mod_h[ttp]2` and `mod_proxy_h[ttp]2` from Apache httpd as a standalone build. 

##Status
**`mod_h[ttp]2` is an official Apache httpd module**, first released in 2.4.17. See [Apache downloads](https://httpd.apache.org/download.cgi) to get a released version. `mod_proxy_h[ttp]2` is part of Apache httpd development, but has not been released yet.

What you find here are **early experience versions** for people who like living on the edge and want to help me test not yet released changes.
>>>>>>> master

If you want HTTP/2 in your production environment, please head over to the official releases at Apache and grab one of those or wait until the various OS distributions have assembled one for you. 

##Current Version
The versions here are **early experience versions**, based on Apache httpd 2-.4.20. There is no guarantee that thises will be released unchanged by Apache. But you are welcome to test it and give feedback.

##Install

You need a built Apache httpd 2.4.20, including apxs and headers to compile and 
run this module. Additionally, you need an installed libnghttp2, at least in version
1.3.0. And additionally, you want an installed OpenSSL 1.0.2.

tl;dr

**You need an installed Apache 2.4.20 which already runs ```mod_http2``` in it.**

If you do not have that or don't know how to get it, look at google, stackoverflow, Apache mailing lists or your Linux distro. Not here!

##Apache 2.4.x Packages

* **Ubuntu**: [ppa by ondrej](https://launchpad.net/~ondrej/+archive/ubuntu/apache2) for Ubuntu 14.04 and others
* **Fedora**: [Rawhide includes httpd 2.4.17](http://rpmfind.net/linux/rpm2html/search.php?query=httpd)
* **Debian** sid (unstable) includes httpd 2.4.17. See [how to install debian sid](https://wiki.debian.org/InstallFAQ#Q._How_do_I_install_.22unstable.22_.28.22sid.22.29.3F)
* **FreeBSD**: [Apache 2.4 port includes mod_http2](http://www.freshports.org/www/apache24/) / [mod_http2-devel port in review](https://reviews.freebsd.org/D5220)

##Changes

See ```ChangeLog``` for details.

##`mod_proxy_http2`

This module is part of the Apache httpd proxy architecture and functions similar to `mod_proxy_http` 
and friends. To configure it, you need to use ```h2:``` or ```h2c:``` in the proxy URL. Example:
(***Important***: against httpd 2.4.20, only ```h2c:``` connections will work!)
```
<Proxy "balancer://h2-local">
    BalancerMember "h2://test.example.org:SUBST_PORT_HTTPS_SUBST"
</Proxy>
<Proxy "balancer://h2c-local">
    BalancerMember "h2c://test.example.org:SUBST_PORT_HTTP_SUBST"
</Proxy>

<IfModule proxy_http2_module>
    ProxyPass "/h2proxy" "balancer://h2-local"
    ProxyPassReverse "/h2proxy" "balancer://h2-local"
    ProxyPass "/h2cproxy" "balancer://h2c-local"
    ProxyPassReverse "/h2cproxy" "balancer://h2c-local"
</IfModule>
```
This will only work under the following conditions:
* the backend speaks HTTP/2, the module will not fallback to HTTP/1.1
* the backend supports HTTP/2 direct mode (see also ```H2Direct``` directive of ```mod_http2```)

All other common httpd ```proxy``` directives also apply.

What it will ***not*** do and what is ***untested***:
* fallback to HTTP/1.1
* support TLS backend connections with Apache httpd 2.4.20 (some necessary changes for ALPN negotiation were not backported)
* be very smart when the number of concurrent streams in the backend differs from the local settings
* load balance between open connections dynamically
* forward any HTTP/2 priority information
* support HTTP/2 PUSH from the backend

What it ***will*** do:
* work with frontend HTTP/1.1 connections
* reuse open HTTP/2 connections from the balancer
* with a frontent HTTP/2 connection, all streams against the same backend will be handled in a single thread.


##Documenation
There is the official [Apache documentation](https://httpd.apache.org/docs/2.4/en/mod/mod_http2.html) of the module, which you will not find here.

I also compiled a [how to h2 in apache](https://icing.github.io/mod_h2/howto.html) document with advice on how to deploy, configure and verify your ```mod_h[ttp]2``` installation.

##Build from git
Still not dissuaded? Ok, here are some hints to get you started.
Building from git is easy, but please be sure that at least autoconf 2.68 is
used:

```
> autoreconf -i
> automake
> autoconf
> ./configure --with-apxs=<path to apxs>
> make
```

##Licensing
Please see the file called LICENSE.


##Credits
This work has been funded by the GSM Association (http://gsma.com). The module
itself was heavily influenced by mod_spdy, the Google implementation of their
SPDY protocol. And without Tatsuhiro Tsujikawa excellent nghttp2 work, this
would not have been possible.


Münster, 19.04.2016,

Stefan Eissing, greenbytes GmbH

Copying and distribution of this file, with or without modification,
are permitted in any medium without royalty provided the copyright
notice and this notice are preserved.  This file is offered as-is,
without warranty of any kind. See LICENSE for details.


