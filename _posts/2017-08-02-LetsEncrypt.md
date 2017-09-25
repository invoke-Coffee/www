---
layout: post
title:  "Setting up Lets encrypt"
date:   2017-08-02 00:00:00 +0000
categories: ssl
---

apt install python-certbot-nginx

Modify nginx to add virtual host

/etc/nginx/sites-enabled/default

        server_name *.invoke.coffee;


certbot --nginx -d www2.invoke.coffee

sudo crontab -e

15 3 * * * /usr/bin/certbot renew --quiet
