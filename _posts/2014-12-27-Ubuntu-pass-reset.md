---
layout: post
title:  "Resetting the root Password on Ubuntu"
date:   2014-12-27 00:00:00 +0000
categories: linux
---
It’s not very common but at some point everyone needs to get back into a Linux box they do not know the password to.

The procedure below is for Ubuntu 14.04 with Grub.

## Reboot the Linux machine

When you get to the Grub screen select the “Advanced options for Ubuntu”

Ubuntu Grub screen

Then the “(recovery mode)” boot option.

Ubuntu Grub Recovery Mode

This will Boot into recovery mode.

The next thing you need to do is mount the File system and read/write.

Select the Network option. This will enable read/write on the boot system.

Ubuntu - Recovery mode - Menu

select “Yes” to enable Read/Write

Ubuntu - Recovery mode - Mount Filesystem

This will bring you back to the “Recovery Menu”

Select “Root” this will drop you unto whats called “single user mode”. From here you have a Shell as root.

Ubuntu - Recovery mode - Root shell

Then set you new password using “passwd”

you can also use “passwd -u myuser” to set the password of any user.

Ubuntu - Recovery mode - passwd

Then exit the shell (exit) and select “resume” from the “Recovery menu”

Ubuntu - Recovery mode - resume

Now you can log in as root with the password you set.
