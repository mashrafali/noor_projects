{{{
Author       : Mohamed Ashraf
Title        : Zenoss Documentation Backup : Server bullet proofing
Version      : 1.0
Creation date: 7/8/2014
Latest update: 7/8/2014
}}}

''The following steps has been created to safeguard the database of All Zenoss varients:''

'''1 - A weekly crontab script has been created @/etc/cron.weekly that does the following'''

a- Performs a live backup of the Databases

b- Dual Compresses the Data-Bases and All RRD Files [Graph Data].

c- SCP the file to backup server @172.17.13.56

d- Cleans original files from the local server of Zenoss, Except for the last backup taken, so only the latest snapshot is stored locally.

Script created:
{{{
#! /bin/bash

echo -en "#### Setting PATH Environment..."
PATH=/opt/zenup/bin:/usr/local/zenoss/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
echo "[OK]"
echo "current time: $(date +%r)"
echo

#setting date
echo
echo -en "#### Setting date..."
when=$(date +%d-%m-%Y)
echo "[OK]"
echo "current time: $(date +%r)"
echo

#PACKING ZODB
echo -en "#### Packing ZODB..."
/usr/local/zenoss/bin/zenossdbpack
echo "[OK]"
echo "current time: $(date +%r)"
echo

#Dumping DB
echo
echo -en "#### Dumping Data Bases to /ZEN-BAKUP..."
mysqldump -uzenoss -pzenoss --single-transaction --routines --triggers --all-databases > /ZEN-BAKUP/ZENOSS-CORP-DB-$when.sql 
echo "[OK]"
echo "current time: $(date +%r)"
echo

#Compressing DB and RRD Directoy
echo
echo "#### Compressing Data..."
tar -cvpzf /ZEN-BAKUP/ZENOSS-CORP-DATA-$when.tgz /ZEN-BAKUP/ZENOSS-CORP-DB-$when.sql /usr/local/zenoss/perf
echo "- Compression Completed Successfully!"
echo "current time: $(date +%r)"
echo

#Removing oiginal file
echo
echo -en "#### cleaning .sql DB file..."
rm /ZEN-BAKUP/ZENOSS-CORP-DB-$when.sql
echo "[OK]"
echo "current time: $(date +%r)"
echo

#Copying over compressed file
echo
echo "#### Copying over compressed file to the Backup Server..."
scp -r /ZEN-BAKUP/ZENOSS-CORP-DATA-$when.tgz root@172.17.13.56:/Backup/Datacenter/zenoss/ZENOSS-CORP
echo "- Copying Completed Successfully!"
echo "current time: $(date +%r)"
echo

#Cleaning 1 week old
echo
echo -en "#### Checking local backup age and cleansing old..."
find /ZEN-BAKUP -type f -mtime +6 -exec rm {} \;
echo "[OK]"
echo "current time: $(date +%r)"
echo
}}}



'''2- Changed the default Ubuntu crontab weekly runtime to execute @friday 9:00AM :'''

@ /etc/crontab
{{{
0  9    * * 5   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
}}}


'''3- On the Backup server'''

a- Created 3 different folders for each of Zenoss varients [Adsl - corp - Systems], for each to receive its respective backup.

b- Added crontab commands to clean backups older than 1 month old.

{{{
00 6 * * *      find /Backup/Datacenter/zenoss/ZENOSS-CORP -type f -mtime +30 -exec rm {} \;
00 7 * * *      find /Backup/Datacenter/zenoss/ZENOSS-SYSTEMS -type f -mtime +30 -exec rm {} \;
00 8 * * *      find /Backup/Datacenter/zenoss/ZENOSS-ADSL -type f -mtime +30 -exec rm {} \;
}}}

'''4- Performed a successful manual execution of the backup script on first zenoss-sys, then zenoss-corp to make sure the script works as intended.'''
 
a- The Restoration process is very simple, first copy over the backed up files from the BACKUP-server

{{{
scp root@172.17.13.56:/Backup/Datacenter/zenoss/ZENOSS-CORP/ZENOSS-CORP-DATA-05-12-2014.tgz /ZEN-BAKUP/
}}}

b- UnCompress the .tgz file

{{{
tar -zxvf /ZEN-BAKUP/ZENOSS-CORP-DATA-05-12-2014.tgz
}}}

c- copy over the RRD files

{{{
cp -r /ZEN-BAKUP/ZENOSS-CORP-DATA-05-12-2014/rrd-folder/* /usr/local/zenoss/perf
}}}

d- Restore MYSQL Databases

{{{
mysql -uroot -p < ZENOSS-CORP-DB.sql
}}}


''' - Manual Database Dump & Restore '''

1- Stop Zenoss

2- Dump the events database

{{{
mysqldump -uzenoss -pzenoss --single-transaction --routines --triggers --all-databases > ZENOSS-CORP-DB.sql
}}}

3- Stop MySQL

4- Take the ibdata/innodb log files/events schema definitions out of /var/lib/mysql, and put them somewhere for safekeeping (in case the subsequent steps didn't work)

5- Restart MySQL

6- Restore the events database dump

{{{
mysql -uroot -p < ZENOSS-CORP-DB.sql
}}}

7- Restart MySQL again (probably unnecessary, but I'm paranoid)

8- Start Zenoss
