---
layout: post
title:  "Update your Shit NTPD edition"
date:   2014-12-20 00:00:00 +0000
categories: ntp
---

As of 18 Dec 2014 NTPD released V 4.2.8 this addressed multiple vulnerability with all previous versions.
## TLDR:

Update all your systems running NTPD (Linux, BSD, Unix based) to V 4.2.8

Use `NTPq -c Version` to display your current version of NTPd

``` sh
Desktop% ntpq -c version
ntpq 4.2.8@1.3265-o Sat Dec 20 02:06:50 UTC 2014 (1)
```
## Am I vulnerable

If you are exposing a NTPd server to the public internet you are vulnerable.

If you are running a NTPd client (most computers/server will be) you are much safer. I am not clear if you are connecting to a malicious NTP server if you could be vulnerable so I would recommend updating or mitigating this possibility.

Update
From the mailing list

If a malicious packet is sent to an ntpd instance from an IP that is
caught by a ‘restrict … noquery’ packet, which has been published BCP
for a while now (which is not to say folks have implemented it), then
that packet will be dropped before any of these exploits could be
attempted.

Please read that sentence carefully.  I’m not sure I have written that
sentence “excellently”.

## What if I can’t update?

If your distribution has not released and update yet you have 2 options.
1. Build from source http://www.ntp.org/downloads.html (Though some issues have been reported with the current build not compiling)
2. Shutdown the service and/or block NTP with a Firewall.


## Full details

From http://support.ntp.org/bin/view/Main/SecurityNotice (Currently down)
### Buffer overflow in crypto_recv() (VE-2014-9295)

- References: Sec 2667 / CVE-2014-9295 / VU#852879
- CVSS: (AV:N/AC:L/Au:N/C:P/I:P/A:P) Base Score: 7.5
- Versions: All releases before 4.2.8
- Date Resolved: Stable (4.2.8) 18 Dec 2014
- Summary: When Autokey Authentication is enabled (i.e. the ntp.conf file contains a crypto pw ... directive) a remote attacker can send a carefully crafted packet that can overflow a stack buffer and potentially allow malicious code to be executed with the privilege level of the ntpd process.
- Mitigation:
  - Upgrade to 4.2.8, or later, from the NTP Project Download Page or the NTP Public Services Project Download Page
  - Disable Autokey Authentication by removing, or commenting out, all configuration directives beginning with the crypto keyword in your ntp.conf file.
  - Credit: This vulnerability was discovered by Stephen Roettger of the Google Security Team.

### Buffer overflow in ctl_putdata()

- References: Sec 2668 / CVE-2014-9295 / VU#852879
- Versions: All NTP4 releases before 4.2.8
- CVSS: (AV:N/AC:L/Au:N/C:P/I:P/A:P) Base Score: 7.5
- Date Resolved: Stable (4.2.8) 18 Dec 2014
- Summary: A remote attacker can send a carefully crafted packet that can overflow a stack buffer and potentially allow malicious code to be executed with the privilege level of the ntpd process.
- Mitigation:
  - Upgrade to 4.2.8, or later, from the NTP Project Download Page or the NTP Public Services Project Download Page
  - Credit: This vulnerability was discovered by Stephen Roettger of the Google Security Team.

### Buffer overflow in configure()

- References: Sec 2669 / CVE-2014-9295 / VU#852879
- Versions: All NTP4 releases before 4.2.8
- Date Resolved: Stable (4.2.8) 18 Dec 2014
- Summary: A remote attacker can send a carefully crafted packet that can overflow a stack buffer and potentially allow malicious code  be - executed with the privilege level of the ntpd process.
- Mitigation:
  - Upgrade to 4.2.8, or later, from the NTP Project Download Page or the NTP Public Services Project Download Page
- Credit: This vulnerability was discovered by Stephen Roettger of the Google Security Team.


### receive(): missing return on error

- References: Sec 2670 / CVE-2014-9296 / VU#852879
- Versions: All NTP4 releases before 4.2.8
- CVSS: (AV:N/AC:L/Au:N/C:N/I:N/A:P) Base Score: 5.0
- Date Resolved: Stable (4.2.8) 18 Dec 2014
- Summary: Code in ntp_proto.c:receive() is missing a return; in the code path where an error was detected, which meant processing d
- not stop when a specific rare error occurred. We haven’t found a way for this bug to affect system integrity. If there is no way
- affect system integrity the base CVSS score for this bug is 0. If there is one avenue through which system integrity can be partial
- affected, the base score becomes a 5. If system integrity can be partially affected via all three integrity metrics, the CVSS ba
- score become 7.5.
- Mitigation:
-     Upgrade to 4.2.8, or later, from the NTP Project Download Page or the NTP Public Services Project Download Page
    -     or Remove or comment out all configuration directives beginning with the crypto keyword in your ntp.conf file.
        - Credit: This vulnerability was discovered by Stephen Roettger of the Google Security Team.
