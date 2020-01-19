{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2015-01-14
Latest update: 2015-01-14
}}}
<<TableOfContents(3)>>

Disable AHBL at local config file of spamassassin
{{{
# vi /opt/zimbra/conf/spamassassin/local.cf
# Add below Line
# disable AHBL, see http://www.ahbl.org/content/changes-ahbl
score DNS_FROM_AHBL_RHSBL 0
}}}

{{{
vi /opt/zimbra/conf/spamassassin/50_scores.cf
# comment below line
#score DNS_FROM_AHBL_RHSBL 0 2.438 0 2.699 # n=0 n=2
}}}

Check 

{{{
# more /opt/zimbra/conf/spamassassin/50_scores.cf | grep AHBL
#score DNS_FROM_AHBL_RHSBL 0 2.438 0 2.699 # n=0 n=2
}}}

Restart Services:

{{{
zimbra@smtp:~$ zmamavisdctl restart && zmmtactl restart
}}}
