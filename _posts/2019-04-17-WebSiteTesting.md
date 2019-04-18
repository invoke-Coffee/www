---
layout: post
title:  "2 ways to test and 7 ways to improve your load times"
date:   2019-04-17 00:00:00 +0000
categories: 
tags: Testing Performance
---

Anyone that has used the internet has been frustrated by slow load times for websites. This can vary from simple annoyance to completely abandoning the site due to how slow it is to load and use. While certain things like the speed of light or bad cell connections is outside of the control of mere mortals, there is many things that can you can to ensure that your website is loading quickly for your users. In this paper we will be going through different things that will affect your website load times, how to test them, and what you can do to fix these.

To start this discussion we will need to look at different ways that you can measure the performance of your website. There is many different moving parts to a complex website and there is also many different ways to go about testing a site. For the purposes of this paper we are going to focus on some simple ways that are also universally applicable. I will be doing these tests from Ubuntu but any *nix os or Windows subsystem for linux will have very similar tools and outputs.

## Ping

First the way that pretty much anyone that started in to technology will learn Ping. Ping is a simple utility that leverages the ICMP protocol to get a simple response from the destination. This can be used to measure the round trip time of a single packet. This simplicity is its strength as well as its weakness. But lets look at a easy example:

``` sh
> ping www.invoke.coffee
PING www.invoke.coffee (104.236.158.192) 56(84) bytes of data.
64 bytes from 104.236.158.192 (104.236.158.192): icmp_seq=1 ttl=51 time=45.4 ms
64 bytes from 104.236.158.192 (104.236.158.192): icmp_seq=2 ttl=51 time=46.4 ms
64 bytes from 104.236.158.192 (104.236.158.192): icmp_seq=3 ttl=51 time=39.10 ms
```

The means that from my home it will take a minimum of 45ms to get to this webserver and back. This only measures the network latency and some websites will block ICMP. Lower this is for your user faster the page will load.

## Curl

The next test we will use is curl. Curl is a command line HTTP client that is perfect for getting the time to download a single page. We can use this with time to measure how long it will take to transfer a single file.

``` sh
>time curl -I https://www.invoke.coffee
HTTP/1.1 200 OK
Server: nginx/1.15.5 (Ubuntu)
Date: Mon, 25 Feb 2019 03:17:11 GMT
Content-Type: text/html
Content-Length: 7680
Last-Modified: Wed, 26 Dec 2018 03:17:31 GMT
Connection: keep-alive
ETag: "5c22f2cb-1e00"
Accept-Ranges: bytes


real    0m0.190s
user    0m0.004s
sys    0m0.012s
```

In this example it will take .19 seconds to load the base page of this site. You can also use this to understand the download time for other assets like images and CSS.

## Physical distance

The first thing to discuss is something that I touched on in the intro. While today's high speed internet connections are extremely fast and getting faster. We will never be able to overcome the simple fact that distance is a factor in how quickly you can download a web page. As you are building a website you need to think about where your primary users are going to be. If it is going to be a website for a physical store or extremely local this will be very specific. But even if you are building a totally online presence you will still be targeting some user base. A simple example is that if your website is in english your user base will be primarily from english speaking countries. As you are looking at hosting providers you should look up where there data centers are located. Most will not specify a physical address but will give you a general location. [Digital Ocean](https://www.digitalocean.com/) for example has data centers is several locations like San Francisco New York or London . Choosing to physically locate your hosting provider to your users is an easy win.

## CDNs

While you can not have your hosting provider close to all clients what you can do is get your content closer to your users. When a web page loads there is the main HTML page that is sent but then the Web browser will load all the assets to take pure HTML page and make it in to your website. This can be Images, CSS, javascript or any sort of static content that your site needs. How most content distribution networks work is that you route all request to a local edge location for that CDN. The first time this will forward the requests to your website and serve the request. The second user will then make the same request. But this time the CDN will have the content cached in there edge location and will serve that content without having to go back to your website. This means that the page load time can be significantly faster. Secondly this reduces the load on your web server. Setting this up will vary on what CDN you use but there is a couple of common parts. On your web server of hosting provider you need to have the Cache control headers inserted into the responses. [RFC 7243](https://tools.ietf.org/html/rfc7234) defines these but for most people the Mozilla developer guide on this is a better resource [Developer.mozilla.org - Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) . The ```Cache-Control: max-age=<seconds>``` header is the most important one. This tells the CDN how long to cache the content before going back to your website to check for updated content. You will also want to ensure that you are NOT caching any dynamic content a good example of this is a login page. If you have a user login then that page is cached the next user would get the first users logon page. Using curl you can check that your website is returning this header.

## Compression

The next thing that we can do is reduce the amount of data that needs to be download. This can be accomplished a couple of different ways. The easiest way and the first thing that we will talk about is HTTP compression. When a web browser goes to make a request to a web server it can specify a range of compression algorithms such as gzip, compress, br, or deflate. Then if the web server supports one of these, the web server can stream compressed data to the client. For text based data such as HTML, CSS, or javascript these algorithms can drastically reduce the effective size of the data that needs to be transferred. Already compressed data like jpgs will not benefit from this. The trade off here is that there is additional work that the Web server needs to do for each request. This will drive up the costs of your web hosting.

You can test if your web server accepts streaming compression with curl. For example we can check [https://www.invoke.coffee](https://www.invoke.coffee) and see that if we specify ```Accept-Encoding: gzip```

``` sh
> curl https://www.invoke.coffee -I -H "Accept-Encoding: gzip"
HTTP/1.1 200 OK
Server: nginx/1.15.5 (Ubuntu)
Date: Mon, 25 Feb 2019 01:32:42 GMT
Content-Type: text/html
Last-Modified: Wed, 26 Dec 2018 03:17:31 GMT
Connection: keep-alive
ETag: W/"5c22f2cb-1e00"
Content-Encoding: gzip
```

The web server will return the content compressed with gzip.

But if we go to [https://www.highline.edu/](https://www.highline.edu/) it does not accept gzip.

``` sh
> curl https://www.highline.edu/ -I -H "Accept-Encoding: gzip"
HTTP/1.1 200 OK
Date: Mon, 25 Feb 2019 01:36:16 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: W3 Total Cache/0.9.5.4
X-Pingback: https://www.highline.edu/xmlrpc.php
Link: <https://www.highline.edu/wp-json/>; rel="https://api.w.org/"
Link: <https://www.highline.edu/>; rel=shortlink
X-TEC-API-VERSION: v1
X-TEC-API-ROOT: https://www.highline.edu/wp-json/tribe/events/v1/
X-TEC-API-ORIGIN: https://www.highline.edu
Cache-Control: max-age=3600
Expires: Mon, 25 Feb 2019 02:36:16 GMT
Content-Type: text/html; charset=UTF-8
```

## Minimize

A related technique is to minimize your code. We add lots of whitespace and comments to HTML, CSS and Javascript to make it easier to read and understand the code. But these have no effect on the browsers ability to render the web page. Transferring this data to the client add no value outside of the random person wanting to see how you did something. A basic website may have a original size of 6,151 bytes. But when Minified it is 5,024. For a savings: 1,127 (18.32%). This was done using [kangax’s minifier](https://kangax.github.io/html-minifier/). As you can see you can get much smaller files without sacrificing any user experience.

## Image sizes

The number and size of the assets that the browsers have to download do matter. This is especially true when the client has a slow internet connection. A good example of this is images. They can be gigabytes in size and this would drastically affect the load speed of your website. For images you will want to compress them using something like the Joint Photographic Experts Group (jpeg) format. This is a lossy image compression format that can drastically reduce the size of the image. This can also compress so far that the image is totally lost. You will need to consider how much compression makes sense for our website and choose accordingly. Using the same curl commands before you can see how much of a difference it will be to reduce the size of these images. But don’t go to far and loose clarity.

-----------

There is many things that make up a web sites page load time and there is also many techniques to improve web performance. A good web developer will keep these techniques in mind and also monitor the performance of there website to ensure that there users have having a good experience.
