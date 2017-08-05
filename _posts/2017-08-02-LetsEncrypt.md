---
layout: post
title:  "Setting up Lets encrypt"
date:   2017-08-02 00:00:00 +0000
categories: ssl
---

These are some quick notes from installing Let's encrypt on a Ubuntu server.

For anyone trying to do this I would recomend the Digital Ocean artical on this. 
[DigitalOcean Let's Encrypt](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)

Install applictions
``` apt install python-certbot-nginx letsencrypt```

Modify nginx to add virtual host
``` sh
/etc/nginx/sites-enabled/default

        server_name *.invoke.coffee;
```


Run Certbot to generate certificate
``` certbot --nginx -d www2.invoke.coffee ```


Modify crontab to renew certificates

``` sudo crontab -e ```

``` 15 3 * * * /usr/bin/certbot renew --quiet ```
