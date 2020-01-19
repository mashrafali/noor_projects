{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-09-24
Latest update: 2014-09-24
}}}
<<TableOfContents(4)>>

== Qmail whitelist IP from IP Range ==
In hsphere you can whitelist an IP from RBL checks by using the following: Edit/Create the file:

{{{
# cp /hsphere/local/var/vpopmail/etc/tcp.smtp /hsphere/local/var/vpopmail/etc/tcp.smtp.bak
}}}
{{{
# vi /hsphere/local/var/vpopmail/etc/tcp.smtp
}}}
Add the following:

{{{
41.187.100.12:allow,RBLSMTPD="",GREETDELAY="3",NULLSENDERMULTRCPTS="1"
127.0.0.1:allow,RELAYCLIENT="",RBLSMTPD="",NULLSENDERMULTRCPTS="1"
}}}
{{{
### Added By Ayman Tohamy ####
#whitelist IP addresses
#IP Range:allow,RELAYCLIENT=""

# Add NOOR Filter.noor.com to Mail3 white list
217.139.224.11:allow,RELAYCLIENT=""

#whitelist RBL IP addresses
#$IP1:allow,RBLSMTPD=""
#$IP2:allow,RBLSMTPD=""
}}}
Then: Recompile via Command Line option

{{{
# cd /hsphere/local/var/vpopmail/etc/
}}}
{{{
# /hsphere/shared/bin/tcprules tcp.smtp.cdb tcp.smtp.tmp < tcp.smtp
}}}
== Google IP Ranges ==
https://support.google.com/a/answer/60764

 . Google Apps mail servers use a large range of IP addresses, and the addresses often change. The most effective means of finding the current range of Google IP addresses is to query Google's SPF record.

{{{
216.239.32.0/19
64.233.160.0/19
66.249.80.0/20
72.14.192.0/18
209.85.128.0/17
66.102.0.0/20
74.125.0.0/16
64.18.0.0/20
207.126.144.0/20
173.194.0.0/16
}}}
{{{
2001:4860:4000::/36
2404:6800:4000::/36
2607:f8b0:4000::/36
2800:3f0:4000::/36
2a00:1450:4000::/36
2c0f:fb50:4000::/36
}}}

{{{
#################
#filter.noor.com#
#################
217.139.224.11:allow,RELAYCLIENT=""
#################
#Google IP Range#
#################
216.239.32.0/19:allow,RELAYCLIENT=""
64.233.160.0/19:allow,RELAYCLIENT=""
66.249.80.0/20:allow,RELAYCLIENT=""
72.14.192.0/18:allow,RELAYCLIENT=""
209.85.128.0/17:allow,RELAYCLIENT=""
66.102.0.0/20:allow,RELAYCLIENT=""
74.125.0.0/16:allow,RELAYCLIENT=""
64.18.0.0/20:allow,RELAYCLIENT=""
207.126.144.0/20:allow,RELAYCLIENT=""
173.194.0.0/16:allow,RELAYCLIENT=""
######################################
}}}
