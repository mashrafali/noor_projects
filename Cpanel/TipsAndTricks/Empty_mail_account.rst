{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Schedule Empty cPanel Mail Account.
Creation date: 2016-03-24
Latest update: 2016-03-24
}}}
 . <<TableOfContents(4)>>

== Empty Mail Account ==

To schedule Empty mailbox spamgrp@elsewedyindustries.com

=== Create Script ===

 .{{{
[~]# vi /usr/local/cpanel/scripts/empty_mailbox
}}}

 .{{{
#!/bin/bash
# remove mail from spamgrp@elsewedyindustries.com email account.
find /home/elsewedy/mail/elsewedyindustries.com/spamgrp/ -type d \( -name cur -o -name new \) -exec find {} -type f -delete \;
}}}



=== Change Permissions ===



 .{{{
# chmod a+x /usr/local/cpanel/scripts/empty_mailbox
}}}



=== Schedule ===

 .{{{
  crontab -e
}}}

 .{{{
0 0 * * 0   /usr/local/cpanel/scripts/empty_mailbox
}}}
