{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-02-13
Latest update: 2014-02-13
}}}
<<TableOfContents(5)>>

= Export Mailboxes =
== Create mailboxlist.txt for all mailboxs: ==
{{{
zmprov -l gaa > /opt/zimbra/backup/mailboxlist.txt
}}}
or

{{{
/opt/zimbra/bin/zmaccts | grep 'active' | egrep -v '^\W+' | awk '{print $1}' > /opt/zimbra/backup/mailboxlist.txt
}}}
Edit file mailboxlist.txt and remove unwanted accounts like

{{{
ham.z6y4waubkt@smtp.noor.net
spam.ldhwhutqaz@smtp.noor.net
galsync.lgfa3krg@smtp.noor.net
virus-quarantine.llvwd0sk@smtp.noor.net
}}}
== Create Mailboxes directory ==
{{{
root@smtp:~# mkdir /opt/zimbra/backup/mailboxes
root@smtp:~# chown zimbra: -R  /opt/zimbra/backup/mailboxes
root@smtp:~# ls -ld /opt/zimbra/backup/mailboxes
drwxr-xr-x 2 zimbra zimbra 4096 Feb  6 19:22 /opt/zimbra/backup/mailboxes
}}}
== Export one Mailbox ==
{{{
zmmailbox -z -m atohamy@noor.net getRestURL "//?fmt=tgz" > /opt/zimbra/backup/mailboxes/atohamy.tgz
}}}
== Export Multiple Mailboxes ==
=== Prepare Export Script ===
{{{
vi /opt/zimbra/backup/export-mailboxes.sh
}}}
{{{
for ACCT in $(cat /opt/zimbra/backup/mailboxlist.txt)
do
echo "$ACCT Exporting starting"
zmmailbox -z -m $ACCT getRestURL "//?fmt=tgz" > /opt/zimbra/backup/mailboxes/${ACCT}.tgz
echo "$ACCT Exporting done"
 done
}}}
=== Make script excutable ===
{{{
root@smtp:~# chmod a+x /opt/zimbra/backup/export-mailboxes.sh
root@smtp:~# chown zimbra:zimbra /opt/zimbra/backup/export-mailboxes.sh
}}}
=== Run Export Script ===
{{{
root@smtp:~# su - zimbra
zimbra@smtp:~$
zimbra@smtp:~$ cd /opt/zimbra/backup/
zimbra@smtp:/opt/zimbra/backup$ ./export-mailboxes.sh
}}}
= Importing Mailboxes =
== Import one mailbox ==
{{{
zmmailbox -z -m atohamy@noor.net postRestURL "//?fmt=tgz&resolve=modify" /opt/zimbra/backup/mailboxes/atohamy@noor.net.tgz
}}}
== To import multiple account ==
=== Prepare below script ===
{{{
vi /opt/zimbra/backup/import-mailboxes.sh
}}}


{{{
for ACCT in $(cat /opt/zimbra/backup/mailboxlist.txt)
do
echo "$ACCT Importing starting"
zmmailbox -z -m $ACCT postRestURL "//?fmt=tgz&resolve=modify" /opt/zimbra/backup/mailboxes/${ACCT}.tgz
echo "$ACCT Importing done"
done
}}}
=== Make script excutable ===
{{{
root@smtp:~# chmod a+x /opt/zimbra/backup/import-mailboxes.sh
root@smtp:~# chown zimbra:zimbra /opt/zimbra/backup/import-mailboxes.sh
}}}
=== Run Import Script. ===
{{{
root@smtp:~# su - zimbra
zimbra@smtp:~$
zimbra@smtp:~$ cd /opt/zimbra/backup/
zimbra@smtp:/opt/zimbra/backup$ ./import-mailboxes.sh
}}}
