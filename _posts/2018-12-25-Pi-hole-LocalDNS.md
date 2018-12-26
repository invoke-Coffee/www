---
layout: post
title:  "Getting Pi-hole to do reverse lookups"
date:   2018-12-25 00:00:00 +0000
categories: jekyll
---

As I was unable to find this anywhere on the web and had to walk back to the solution from dnsmasq I thought that some others may find this interesting. 

### The problem
Many users of Pi-hole would the built in DHCP server. But for those like me that have a complex (ok its unnecessarily complex) you may want to use a separate DHCP server that would be authoritative for you local lan. But if you do this the Dashboard will  now only show IP addresses. 

### The easy stuff
If the pihole is on the same LAN as all your clients you can simply configure a *Conditional Forwarding* IP address in **Settings -> DNS** and uncheck the *Never forward reverse lookups for private IP ranges*

But if you are like me I want to have separate networks on my lan so this will not resolve the other networks.

### Adding additional arpa zones.

If you look at **/etc/pihole/setupVars.conf** you will find the following line (or similar). 
```
CONDITIONAL_FORWARDING_REVERSE=10.168.192.in-addr.arpa
```
This will forward all reverse lookups for 192.168.10.0/24 IPs but not any other networks. 


So by editing this to ```server=/168.192.in-addr.arpa/192.168.10.1``` This will forward lookups for all 192.168.0.0/16 and for me this covers all of my networks. 


Then all you have to do is run a **pihole -r** and run repair to update dnsmasq.


Hope this helps the next person that runs into this. 
