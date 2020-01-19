{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-02-13
Latest update: 2014-02-13
}}}
<<TableOfContents(3)>>

= Export Filters =
== create folder ==
{{{
    mkdir /opt/zimbra/backup/filters
}}}
{{{
    chown -R zimbra:zimbra /opt/zimbra/backup/filters
}}}
== Change permissions ==
{{{
    root@smtp:~# touch /usr/bin/export-users-filters.sh
    root@smtp:~# chown -R zimbra:zimbra /usr/bin/export-users-filters.sh
    chmod +x  /usr/bin/export-users-filters.sh
}}}
== Export all users filters ==
{{{
vi /usr/bin/export-users-filters.sh
}}}
{{{
/opt/zimbra/bin/zmaccts | grep 'active' | egrep -v '^\W+' | awk '{print $1}' > /opt/zimbra/backup/filters/allaccounts.txt
for ACCT in $(cat /opt/zimbra/backup/filters/allaccounts.txt)
do
echo $ACCT
 zmprov ga $ACCT zimbraMailSieveScript > /opt/zimbra/backup/filters/${ACCT}_rules.sieve
 chmod +x /opt/zimbra/backup/filters/${ACCT}_rules.sieve
 done
}}}
= Import Filters =
== Modify filters berfore IMPORTING ==
=== Modify exported filters for one user ===
Go to the path which you have export user (i.e. atohamy@noor.net_rules.sieve ) filters then run below commands to edit user's filters before importing it:

Copy fiters directory before editing them:

{{{
cp -r /opt/zimbra/backup/filters /opt/zimbra/backup/filters.modified
cd /opt/zimbra/backup/filters.modified
}}}
{{{
sed -i 's/#\ name/zmprov ma/g' atohamy\@noor.net_rules.sieve
}}}
{{{
sed -i 's/zimbraMailSieveScript:\ require/zimbraMailSieveScript\ require'"'"'/g'  atohamy\@noor.net_rules.sieve
}}}
{{{
echo "'" >> atohamy\@noor.net_rules.sieve
}}}
=== Modify exported filters for multiple users ===
For all files at work directory

{{{
cp -r /opt/zimbra/backup/filters /opt/zimbra/backup/filters.modified
}}}
{{{
cd /opt/zimbra/backup/filters.modified
}}}
{{{
sed -i 's/#\ name/zmprov ma/g' *.sieve
}}}
{{{
sed -i '/^zmprov/ s/$/ \\/' *.sieve
}}}
{{{
sed -i 's/zimbraMailSieveScript:\ require/zimbraMailSieveScript\ require'"'"'/g'  *.sieve
}}}
{{{
for f in *.sieve ; do echo "'" >> $f ; done
}}}
== Import Filters to new server ==
=== Remove non employees accounts ===
{{{
Recomended to remove these filters before importing:
}}}
{{{
ls  | grep -v "@noor.net"
aali@nooradsl.com_rules.sieve
admin@smtp.noor.net_rules.sieve
cs@nooradsl.com_rules.sieve
galsync.76r3mjmqp@smtp.noor.net_rules.sieve
ham.7gxnsmm5ag@smtp.noor.net_rules.sieve
spam.oggpw720fk@smtp.noor.net_rules.sieve
virus-quarantine.sgwcnhnea@smtp.noor_rules.sieve
}}}
{{{
rm -rf galsync.76r3mjmqp@smtp.noor.net_rules.sieve
rm -rf ham.7gxnsmm5ag@smtp.noor.net_rules.sieve
rm -rf spam.oggpw720fk@smtp.noor.net_rules.sieve
rm -rf virus-quarantine.sgwcnhnea@smtp.noor_rules.sieve
}}}
=== Copy and  change permissions ===
{{{
    scp -r /opt/zimbra/backup/filters.modified root@192.168.0.85:/opt/zimbra/backup/
    chmod +x /opt/zimbra/backup/filters.modified/*.sieve
    chown -R zimbra: /opt/zimbra/backup/filters.modified/
}}}
=== Run filters to be imported ===
{{{
    cd /opt/zimbra/backup/filters.modified
    for x in *.sieve ; do ./$x ; done
}}}
