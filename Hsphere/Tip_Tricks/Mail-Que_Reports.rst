{{{
Author       : Mohamed Ashraf
Title        : Reporting Mail Queues
Version      : 1.0
Creation date: 23/10/2014
Latest update: 23/10/2014
}}}


'''''The following Scripts has been created to create mail reports for mail queues'''''

<<TableOfContents(4)>>


== NOOR Filter (NSCAN) ==

{{{

root@nscan:~/server-status# cat hourly-queue.sh 
#! /bin/bash


## Building Report

cd /root/server-status
echo "SUBJECT: [NOOR SCAN] Hourly Queue Report" > queue-body.txt
echo "TO: mashraf@noor.net " >> queue-body.txt
echo "TO: hhh@noor.net " >> queue-body.txt
echo "CC: " >> queue-body.txt
echo >> queue-body.txt
echo "                                      NOOR-SCAN Hourly Queue Report" >> queue-body.txt
echo >> queue-body.txt
echo -en "Snapshot date : " >> queue-body.txt
echo $(date +"%A %d %B %Y [%r]") >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
echo "-- TOTAL NUMBER OF MAILS IN QUEUE : $(mailq | grep Requests | cut -d" " -f5)" >> queue-body.txt 
echo >> queue-body.txt
echo >> queue-body.txt
#echo "Domains that are Queuing":  >> queue-body.txt
#echo "------------------------":  >> queue-body.txt
#qshape >> queue-body.txt
#echo >> queue-body.txt
#echo >> queue-body.txt
#echo "############################################### QUEUED MAILS ##########################################" >> queue-body.txt
#echo >> queue-body.txt
#echo >> queue-body.txt
#mailq >> queue-body.txt
#echo >> queue-body.txt
#echo >> queue-body.txt

sendmail mashraf@noor.net < queue-body.txt

}}}

- and then add script to crontab

{{{

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
#REPORTS
0 * * * * /root/server-status/hourly-queue.sh

}}}

== Smtp.noor.net ==

{{{

#! /bin/bash


## Building Report

cd /root/server-status
echo "SUBJECT: [SMTP.NOOR.NET] Daily Queue Report" > queue-body.txt
echo "TO: hhh@noor.net " >> queue-body.txt
echo "TO: mashraf@noor.net " >> queue-body.txt
echo "CC: " >> queue-body.txt
echo >> queue-body.txt
echo "                                      Smtp.noor.net Queue Report" >> queue-body.txt
echo >> queue-body.txt
echo -en "Snapshot date : " >> queue-body.txt
echo $(date +"%A %d %B %Y [%r]") >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
su - zimbra -c 'echo "-- TOTAL NUMBER OF MAILS IN QUEUE : $(mailq | grep Requests | cut -d" " -f5)"'  >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
#echo "############################################### QUEUED MAILS ##########################################" >> queue-body.txt
#echo >> queue-body.txt
#echo >> queue-body.txt
#qmHandle -l >> queue-body.txt
#echo >> queue-body.txt
#echo >> queue-body.txt

sendmail  hhh@noor.net < queue-body.txt

}}}

- and then add script to crontab

{{{

SHELL=/bin/bash
MAIL=/var/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
HOME=/root


# m h  dom mon dow   command                 minute (0-59), hour (0-23, 0 = midnight), day (1-31), month (1-12), weekday (0-6, 0 = Sunday), command


# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
#REPORTS

0 7 * * * /root/server-status/mail-que-report.sh

}}}

== Mail.noor.com ==

{{{

[root@mail server-status]# cat mail-que-report.sh 
#! /bin/bash


## Building Report

cd /root/server-status
echo "SUBJECT: [MAIL.NOOR.COM] Daily Queue Report" > queue-body.txt
echo "TO: hhh@noor.net " >> queue-body.txt
echo "CC: " >> queue-body.txt
echo >> queue-body.txt
echo "                                      Mail.noor.com Queue Report" >> queue-body.txt
echo >> queue-body.txt
echo -en "Snapshot date : " >> queue-body.txt
echo $(date +"%A %d %B %Y [%r]") >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
echo "-- TOTAL NUMBER OF MAILS IN QUEUE :" >> queue-body.txt 
qmHandle -L >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
echo "############################################### QUEUED MAILS ##########################################" >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
qmHandle -l >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt

sendmail mashraf@noor.net hhh@noor.net < queue-body.txt

}}}

- Then Add Script to Crontab

{{{

SHELL=/bin/bash
MAIL=/var/spool/mail/root
PATH=/hsphere/shared/bin:/hsphere/shared/sbin:/hsphere/local/var/vpopmail/bin:/usr/local/bin:/usr/local/sbin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
HOME=/root


# m h  dom mon dow   command                 minute (0-59), hour (0-23, 0 = midnight), day (1-31), month (1-12), weekday (0-6, 0 = Sunday), command


# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
#REPORTS
0 7 * * * /root/server-status/mail-que-report.sh

}}}

== Mail3.noor.com ==

{{{

[root@mail3 server-status]# cat mail-que-report.sh 
#! /bin/bash


## Building Report

cd /root/server-status
echo "SUBJECT: [MAIL3.NOOR.COM] Daily Queue Report" > queue-body.txt
echo "TO: mashraf@noor.net " >> queue-body.txt
echo "CC: " >> queue-body.txt
echo >> queue-body.txt
echo "                                      Mail3.noor.com Queue Report" >> queue-body.txt
echo >> queue-body.txt
echo -en "Snapshot date : " >> queue-body.txt
echo $(date +"%A %d %B %Y [%r]") >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
echo "-- TOTAL NUMBER OF MAILS IN QUEUE :" >> queue-body.txt
qmHandle -L >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
echo "############################################### QUEUED MAILS ##########################################" >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
qmHandle -l >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt

sendmail hhh@noor.net < queue-body.txt

}}}

- Then Add Script To Crontab

{{{

SHELL=/bin/bash
MAIL=/var/spool/mail/root
PATH=/hsphere/shared/bin:/hsphere/shared/sbin:/hsphere/local/var/vpopmail/bin:/usr/local/bin:/usr/local/sbin:/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/r$
HOME=/root


# m h  dom mon dow   command                 minute (0-59), hour (0-23, 0 = midnight), day (1-31), month (1-12), weekday (0-6, 0 = Sunday), command


# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
#REPORTS

0 7 * * * /root/server-status/mail-que-report.sh

}}}

== Mail.noor.net ==

{{{

#! /bin/bash


## Building Report

cd /root/server-status
echo "SUBJECT: [MAIL.NOOR.NET] Daily Queue Report" > queue-body.txt
echo "TO: hhh@noor.net " >> queue-body.txt
echo "TO: mashraf@noor.net " >> queue-body.txt
echo "CC: " >> queue-body.txt
echo >> queue-body.txt
echo "                                      Mail.noor.net Queue Report" >> queue-body.txt
echo >> queue-body.txt
echo -en "Snapshot date : " >> queue-body.txt
echo $(date +"%A %d %B %Y [%r]") >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
su - zimbra -c 'echo "-- TOTAL NUMBER OF MAILS IN QUEUE : $(mailq | grep Requests | cut -d" " -f5)"'  >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
#echo "############################################### QUEUED MAILS ##########################################" >> queue-body.txt
#echo >> queue-body.txt
#echo >> queue-body.txt
#qmHandle -l >> queue-body.txt
#echo >> queue-body.txt
#echo >> queue-body.txt

sendmail  hhh@noor.net < queue-body.txt

}}}

- Then Add Script To Crontab

{{{

SHELL=/bin/bash
MAIL=/var/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
HOME=/root


# m h  dom mon dow   command                 minute (0-59), hour (0-23, 0 = midnight), day (1-31), month (1-12), weekday (0-6, 0 = Sunday), command


# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
#REPORTS

0 7 * * * /root/server-status/mail-que-report.sh

}}}

== mail.elsewedyind.com ==

{{{

root@mail [~/server-status]# cat mail-que-report.sh 
#! /bin/bash


## Building Report

cd /root/server-status
echo "SUBJECT: [mail.elsewedyind.com] Daily Queue Report" > queue-body.txt
echo "TO: hhh@noor.net " >> queue-body.txt
echo "CC: " >> queue-body.txt
echo >> queue-body.txt
echo "                                      mail.elsewedyind.com Queue Report" >> queue-body.txt
echo >> queue-body.txt
echo -en "Snapshot date : " >> queue-body.txt
echo $(date +"%A %d %B %Y [%r]") >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
echo -en "-- TOTAL NUMBER OF MAILS IN QUEUE : " >> queue-body.txt 
exim -bpc >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
echo "############################################### QUEUED MAILS ##########################################" >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt
exim -bp >> queue-body.txt
echo >> queue-body.txt
echo >> queue-body.txt

sendmail hhh@noor.net < queue-body.txt
}}}

- Then Add Script To Crontab

{{{

SHELL=/bin/bash
MAIL=/var/spool/mail/root
PATH=/usr/local/jdk/bin:/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/bin:/usr/X11R6/bin:/root/bin
HOME=/root


# m h  dom mon dow   command                 minute (0-59), hour (0-23, 0 = midnight), day (1-31), month (1-12), weekday (0-6, 0 = Sunday), command


# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
#REPORTS
0 7 * * * /root/server-status/mail-que-report.sh

}}}
