{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-02-13
Latest update: 2014-02-13
}}}
<<TableOfContents(3)>>

Create list for all zimbra mailboxs

{{{
zmprov -l gaa > /opt/zimbra/backup/mailboxlist.txt
}}}

or 

{{{
/opt/zimbra/bin/zmaccts | grep 'active' | egrep -v '^\W+' | awk '{print $1}' > /opt/zimbra/backup/mailboxlist.txt
}}}
