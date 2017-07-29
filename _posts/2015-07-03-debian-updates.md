---
layout: post
title:  "Automatic updates with Debian"
date:   2015-07-03 00:00:00 +0000
categories: linux
---

Though I am taking some risk by automatically updating my servers. I prefer that they always update so that security patches are applied very quickly. Here is how I configured my Debian servers (jessie) to update on a daily bases.

I created a shell script in /root/update.sh
``` sh
apt-get update -y > /root/update.log
apt-get upgrade -y > /root/update.log
```

very simple update and upgrade outputting to a log file.
``` Chmod 755 /root/update.sh ```

set the sh as executable.

Then used crontab to set up the sh to run daily.
``` sh
crontab -e
# m     h       dom     mon     dow     command
00      08      *       *       *       /root/update.sh
```
Waited for the cron job to run and checked update.log
``` sh
Hit http://mirrors.digitalocean.com jessie InRelease
Hit http://mirrors.digitalocean.com jessie/main amd64 Packages
Hit http://mirrors.digitalocean.com jessie/main Translation-en
Hit http://security.debian.org jessie/updates InRelease
Hit http://security.debian.org jessie/updates/main amd64 Packages
Hit http://security.debian.org jessie/updates/main Translation-en
Reading package lists…
Reading package lists…
Building dependency tree…
Reading state information…
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
sweet looks good.

---

Clearly there is some limitations to this. I have to check the log to see any failures and I will get all updates not just security updates.

Logwatch will monitor the basic updates but I could certainly shot myself in the foot with an update
