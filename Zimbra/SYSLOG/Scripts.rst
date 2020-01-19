Describe Enterprise/IT/Docs/HowTo/Linux/Zimbra/SYSLOG/Scripts here.

{{{
Author       : Mahmoud Hamdy 
Version      : 1.0
Creation date: 5-11-2014
}}}

<<TableOfContents(3)>>

== Scripts for cleaning the logs directories for syslog ==

{{{

cat /dev/null > /var/log/mail.log

cat /dev/null > /opt/zimbra/log/mailbox.log

We cleaning the logs of syslog every 1 hour

}}}


== scripts for cleaning the team accounts and rerun the filters ==

{{{
for  name  in  $(cat /opt/zimbra/backup/scripts/ets1users)
do
su - zimbra -c "zmmailbox -z -m $name@noornms.net emptyfolder inbox"
sed "s|\ets1|${name}|"  /opt/zimbra/backup/ets1filters.sieve  >  /tmp/file1
chmod +x /tmp/file1
su - zimbra -c "/tmp/file1"
done


NOTE1: We write the ets1 team members in ets1users file
NOTE2: We run this script every week to empty the user accounts and rerun the filters on the team members

}}}
