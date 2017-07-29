---
layout: post
title:  "rsyslog failure"
date:   2015-06-27 00:00:00 +0000
categories: Linux
---
I ran into an issue with rsyslog on debian.

when running apt-get install (in this case git) I would get this back.
``` sh
Setting up git (1:2.1.4-2.1) …
Errors were encountered while processing:
rsyslog
fail2ban
E: Sub-process /usr/bin/dpkg returned an error code (1)
```
So I removed fail2ban
``` sh
sudo apt-get remove fail2ban
Reading package lists… Done
Building dependency tree
Reading state information… Done
The following packages were automatically installed and are no longer required:
gamin libcurl3 libgamin0 liblognorm1 libpython3.4 libvpx1 python-gamin python-pyinotify python3-pyinotify python3-systemd
Use ‘apt-get autoremove’ to remove them.
The following packages will be REMOVED:
fail2ban
0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.
2 not fully installed or removed.
After this operation, 975 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database … 39357 files and directories currently installed.)
Removing fail2ban (0.9.2-1) …
Processing triggers for man-db (2.7.0.2-5) …
Setting up rsyslog (8.9.0-3) …
Job for rsyslog.service failed because the control process exited with error code. See “systemctl status rsyslog.service” and “journalctl -xe” for details.
invoke-rc.d: initscript rsyslog, action “restart” failed.
dpkg: error processing package rsyslog (–configure):
subprocess installed post-installation script returned error exit status 1
Errors were encountered while processing:
rsyslog
E: Sub-process /usr/bin/dpkg returned an error code (1)
```
Hm but that’s still annoying. But hey I get some idea on what to check.
``` sh
systemctl status rsyslog.service
● rsyslog.service – System Logging Service
Loaded: loaded (/lib/systemd/system/rsyslog.service; enabled; vendor preset: enabled)
Active: failed (Result: start-limit) since Sat 2015-06-27 21:55:09 PDT; 22s ago
Docs: man:rsyslogd(8)
http://www.rsyslog.com/doc/
Process: 32585 ExecStart=/usr/sbin/rsyslogd -n (code=exited, status=1/FAILURE)
Main PID: 32585 (code=exited, status=1/FAILURE)
```
Ok now we are getting somewhere.

lets remove rsyslog.
``` sh
sudo dpkg -r rsyslog
(Reading database … 39129 files and directories currently installed.)
Removing rsyslog (8.9.0-3) …
Processing triggers for man-db (2.7.0.2-5) …
```
Well that works kinda.

Then I found this

https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=724796

Yep looks like a bug. But easy workaround

Kill existing rsyslog process.
``` sh
ps aux | grep rsyslog
User    2744  0.0  0.0  12708   976 pts/0    S+   22:17   0:00 grep rsyslog
Kill 2744
```
and restart
``` sh
sudo invoke-rc.d rsyslog restart
```
and take a look back at
``` sh
sudo journalctl -xe
Jun 27 22:10:59 Invoke.coffee systemd[1]: Reloading.
Jun 27 22:10:59 Invoke.coffee systemd[1]: [/etc/systemd/system/multi-user.target.wants/digitalocean-rc-local.service:18] Support for option SysVStartPriority= has been removed and it is ignored
Jun 27 22:10:59 Invoke.coffee systemd[1]: avahi-daemon.socket: Cannot add dependency job, ignoring: Unit avahi-daemon.socket is masked.
Jun 27 22:10:59 Invoke.coffee systemd[1]: Started ACPI event daemon.
— Subject: Unit acpid.service has finished start-up
— Defined-By: systemd
— Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
—
— Unit acpid.service has finished starting up.
—
— The start-up result is done.
Jun 27 22:10:59 Invoke.coffee systemd[1]: Stopped System Logging Service.
— Subject: Unit rsyslog.service has finished shutting down
— Defined-By: systemd
— Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
```

Alright we are good to go.
