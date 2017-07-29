---
layout: post
title:  "Keeping FreeBSD up to date"
date:   2015-01-19 00:00:00 +0000
categories: freeBSD
---
FreeBsd makes keeping your system up-to-date relatively easy using a utility called freebsd-update.

simply run

```freebsd-update fetch```


to download all updates

then run

```freebsd-update install```


To install the downloaded updates

When it comes time to upgrade to a new major version of FreeBSD you will need to use upgrade and specify the release you are upgrading to.

```freebsd-update -r 10.1-RELEASE upgrade``` download and configure the new version

```freebsd-update install``` start the install of the new version

```shutdown -r now``` reboot your system to load the new kernel

```freebsd-update install``` Finish the installs

There may be some questions that you are asked but for the most part you can accept the defaults

more information can be found in the freebsd-handbook

https://www.freebsd.org/doc/en/books/handbook/updating-upgrading-freebsdupdate.html
