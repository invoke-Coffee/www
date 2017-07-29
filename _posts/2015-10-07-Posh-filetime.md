---
layout: post
title:  "Powershell Converting from Filetime"
date:   2015-10-07 00:00:00 +0000
categories: Powershell
---
If you have ever worked with Powershell you send up running into DateTime stamps that are stored in FileTime instead of DateTime.

You can easily convert these to a date time object with the DateTime type accelerator.
``` Powershell
PS C:\> [datetime]::FromFileTime(129948127853609000)
Monday, October 15, 2012 3:13:05 PM
```

If you have ever wondered FileTime is a 64-Bit value representing the number of 100-nanosecond intervals since January 1, (UTC)
Reference
