## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Cpanel/TipsAndTricks/Sending_mail_message_limit
{{{
Author       : Ayman Tohamy
Version      : 1.1
Title        : cPanel sending mail limit
Creation date: 2015-07-08
Latest update: 2015-07-08
}}}
<<TableOfContents(3)>>


== To Backup Exim  ==
{{{
Home »Service Configuration »Exim Configuration Manager>> Advanced Editor

- From Backup >> Backup Exim configuration on server before any change
}}}

== To set attachment message size limit ==
{{{
 cPanel WHM:
Home »Service Configuration »Exim Configuration Manager>> Advanced Editor

Go to Button "Add additional configuration setting" > press + Button
Choose >> message_size_limit  >> 30M
}}}

{{{
cpanel [~]# cat /etc/exim.conf | grep message_size_limit 
message_size_limit = 30M

cpanel [~]# cat /etc/exim.conf.local | grep message_size_limit
message_size_limit = 30M
}}}

== To Limit max number of messages per hour per domain ==
{{{

cPanel WHM  >> Home »Server Configuration »Tweak Settings

Max hourly emails per domain >> 250
}}}

per cpanel account
https://forums.cpanel.net/threads/max-emails-limit-is-per-domain-or-per-cpanel-account.199891/

== To Limit number of recipients per message ==
{{{
 cPanel WHM:
Home »Service Configuration »Exim Configuration Manager>> Advanced Editor

Go to Button "Add additional configuration setting" > press + Button
Choose >> recipients_max_reject  >> true
Choose >> recipients_max  >> 100
}}}

{{{
cpanel [~]# cat /etc/exim.conf | grep recipients_max 
recipients_max_reject = true
recipients_max = 100
 
cpanel [~]# cat /etc/exim.conf.local | grep recipients_max
recipients_max_reject = true
recipients_max = 100
}}}

== To limit number of messages daily per email ==

http://www.directadmin.com/features.php?id=1246
https://forums.cpanel.net/threads/daily-mail-sending-limits.51581/
