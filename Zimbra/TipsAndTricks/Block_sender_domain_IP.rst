{{{
Author       : Ayman Tohamy
Version      : 1.1
Title         : Block Zimbra sender address , Domain or IP address
Creation date: 2016-08-16
Latest update: 2016-08-16
}}}
<<TableOfContents(3)>>

== Block Sender or domain ==

1- Check Zimbra Version:

{{{
zimbra@mail:~$ zmcontrol -v
Release 8.6.0.GA.1153.UBUNTU14.64 UBUNTU14_64 FOSS edition.
}}}

ZCS 7
{{{
zmlocalconfig -e postfix_smtpd_sender_restrictions="check_sender_access hash:/opt/zimbra/postfix/conf/postfix_reject_sender"
}}}

ZCS 8.0
{{{
Add "check_sender_access hash:/opt/zimbra/postfix/conf/postfix_reject_sender" as the first line of /opt/zimbra/conf/zmconfigd/smtpd_sender_restrictions.cf
}}}

ZCS 8.5 and 8.6


A- Create the postmap database as defined below 

Modify /opt/zimbra/conf/zmconfigd/smtpd_sender_restrictions.cf, by adding this as the second line of the file:

{{{
zimbra@mail:~$ vim /opt/zimbra/conf/zmconfigd/smtpd_sender_restrictions.cf  
}}}

{{{
%%contains VAR:zimbraMtaSmtpdSenderRestrictions check_sender_access lmdb:/opt/zimbra/postfix/conf/postfix_reject_sender%%
}}}

B- Then execute:

zmprov ms <zmhostname> +zimbraMtaSmtpdSenderRestrictions "check_sender_access lmdb:/opt/zimbra/postfix/conf/postfix_reject_sender"

example:
{{{
zimbra@mail:~$ zmprov ms mail.noor.net +zimbraMtaSmtpdSenderRestrictions "check_sender_access lmdb:/opt/zimbra/postfix/conf/postfix_reject_sender" 
}}}

C- Create file /opt/zimbra/postfix/conf/postfix_reject_sender with the list of email addresses and domains to be rejected in the below format:

{{{
zimbra@mail:~$ vim /opt/zimbra/postfix/conf/postfix_reject_sender
  user@domain.com REJECT
  domainX.com REJECT
  example.top REJECT
  top REJECT
}}}

D- postmap it and restart postfix

{{{
zimbra@mail:~$ /opt/zimbra/postfix/sbin/postmap /opt/zimbra/postfix/conf/postfix_reject_sender

zimbra@mail:~$  zmmtactl stop && zmmtactl start

/postfix-script: stopping the Postfix mail system
Stopping saslauthd...done.
Rewriting configuration files...done.
Starting saslauthd...done.
/postfix-script: starting the Postfix mail system
}}}


E- Verification

Check the Postfix configuration with postconf | grep smtpd_sender_restrictions

{{{
zimbra@mail:~$ postconf | grep smtpd_sender_restrictions
smtpd_sender_restrictions = check_sender_access lmdb:/opt/zimbra/postfix/conf/postfix_reject_sender, 

}}}


You'll be able to see the changes show up in /opt/zimbra/log/zmconfigd.log .

Reject messages will be logged in /var/log/zimbra.log ; format looks like this:

[date / hostname] postfix/smtpd[####] NOQUEUE: reject: RCPT from [remote mta]: 554 5.7.1 <senders-email@DOMAIN>:
Sender address rejected: Access denied: from=<senders-email@DOMAIN> to=<local-zimbra-user@domain> proto=ESMTP helo=<remote mta>

{{{
zimbra@mail:~$ grep "Sender address rejected: Access denied" /var/log/zimbra.log  | tail  
                        
Aug 16 12:31:41 mail postfix/smtpd[17918]: NOQUEUE: reject: RCPT from unknown[217.139.224.32]: 554 5.7.1 <username@example.top>: 
Sender address rejected: Access denied; from=<username@example.top> to=<ayman.tohamy@yahoo.com> proto=SMTP helo=<atohamy>
}}}
The sender will receive a returned email declaring the rejection. 


== Blacklist specific IP addresses ==
##############################################


Starting with ZCS 8.5 and later, it is now possible to maintain an IP blacklist for connections to Postfix. This is useful in DOS and targeted spam attack scenarios.

1- Edit /opt/zimbra/conf/postfix_blacklist

{{{
zimbra@mail:~$ vim /opt/zimbra/conf/postfix_blacklist
}}}

2- Add IP address SPACE REJECT to the file, one IP address per line

{{{
    1.2.3.4 REJECT
    217.139.224.32 REJECT
}}}

2- postmap /opt/zimbra/conf/postfix_blacklist

{{{
zimbra@mail:~$ postmap /opt/zimbra/conf/postfix_blacklist
}}}

3- zmprov mcf +zimbraMtaRestriction 'check_client_access lmdb:/opt/zimbra/conf/postfix_blacklist'

{{{
zimbra@mail:~$ zmprov mcf +zimbraMtaRestriction 'check_client_access lmdb:/opt/zimbra/conf/postfix_blacklist'
}}}

4- Verify 

{{{
zimbra@mail:~$ grep "Client host rejected: Access denie" /var/log/zimbra.log  | tail
        
Aug 16 14:20:54 mail postfix/smtpd[28690]: NOQUEUE: reject: RCPT from unknown[217.139.224.32]: 554 5.7.1 <unknown[217.139.224.32]>: Client host rejected: Access denied; from=<test@noorservices.org> to=<ayman.tohamy@yahoo.com> proto=SMTP helo=<atohamy>

Aug 16 14:21:00 mail postfix/smtpd[28690]: NOQUEUE: reject: RCPT from unknown[217.139.224.32]: 554 5.7.1 <unknown[217.139.224.32]>: Client host rejected: Access denied; from=<test@noorservices.org> to=<ayman.tohamy@yahoo.com> proto=SMTP helo=<atohamy>

}}}
Note:
{{{
- postmap will need to be re-run on the file anytime an IP address is added or removed. 
}}}

== References: ==
{{{
https://wiki.zimbra.com/wiki/New_Features_ZCS_8.5#Ability_to_blacklist_specific_IP_addresses
https://wiki.zimbra.com/wiki/Restrict_users_to_certain_domain
https://wiki.zimbra.com/wiki/Domain_level_blocking_of_users
https://bugzilla.zimbra.com/show_bug.cgi?id=90050
http://linux-sys-adm.com/how-to-restrict-users-sending-to-certain-usersdomains-zimbra-8.6-on-ubuntu-server-14.04-lts-step-by-step/
}}}
