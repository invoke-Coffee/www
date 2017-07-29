---
layout: post
title:  "Dovecot autocreate plugin to mailbox { auto }"
date:   2015-07-03 00:00:00 +0000
categories: linux
---

With dovecot 2.2 Autocreate was deprecated. and the very helpful message started popping up in my logs
```
Warning: autocreate plugin is deprecated, use mailbox { auto } setting instead
```

Dovecot’s wiki does have a page on this http://wiki2.dovecot.org/MailboxSettings but its not very helpful.

So here is what I actually did to move from autocreate to mailbox { auto }

First comment out autocreate in ```/etc/dovecot/dovecot.conf```
``` sh
#deprecated using mailbox auto
#protocol imap {
#  mail_plugins = ” autocreate”
#}
#plugin {
#  autocreate = Trash
#  autocreate2 = Sent
#  autosubscribe = Trash
#  autosubscribe2 = Sent
#}
```
second open up ```/etc/dovecot/conf.d/10-main.conf```

In the namespace inbox section I added to new auto function. I decided to keep in simple and just use the wiki’s example. so now it has (some comments removed)
``` sh
namespace inbox {

# There can be only one INBOX, and this setting defines which namespace
# has it.
inbox = yes

#mailbox auto create
mailbox Trash {
auto = no
special_use = \Trash
}
mailbox Drafts {
auto = no
special_use = \Drafts
}
mailbox Sent {
auto = subscribe # autocreate and autosubscribe the Sent mailbox
special_use = \Sent
}
mailbox “Sent Messages” {
auto = no
special_use = \Sent
}
mailbox Spam {
auto = create # autocreate Spam, but don’t autosubscribe
special_use = \Junk
}
mailbox virtual/All { # if you have a virtual “All messages” mailbox
auto = no
special_use = \All
}

}
```

restarted dovecot and tested.


Hope this helps someone else along the way.
