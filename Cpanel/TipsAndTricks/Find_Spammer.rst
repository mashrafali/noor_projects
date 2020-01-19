{{{
Author       : Ayman Tohamy
Version      : 1.1
Title        : How to find spammer in cPanel server.
Creation date: 2015-12-27
Latest update: 2016-02-24
}}}
<<TableOfContents(3)>>

== Exim – Scripts to find the Origin of Spam mails in cPanel Server ==
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

1- To get a sorted list of email sender in exim mail queue. It will show the number of mails send by each one.

{{{
# exim -bpr | grep "<" | awk {'print $4'} | cut -d "<" -f 2 | cut -d ">" -f 1 | sort -n | uniq -c | sort -n
}}}
You will get a result as like follows,

{{{
1       ventas@testdomain.com
2       sales@test1domain.com
3       sandy@test123.com
4       root@testdomain.co.in
29     admin@testdomain.in
124   albert@test123domain.com
}}}
The following scripts will check the script that will originate spam mails:

{{{
# grep "cwd=/home" /var/log/exim_mainlog | awk '{for(i=1;i<=10;i++){print $i}}' | sort | uniq -c | grep cwd | sort -n

# awk '{ if ($0 ~ "cwd" && $0 ~ "home") {print $3} }' /var/log/exim_mainlog | sort | uniq -c | sort -nk 1

# grep 'cwd=/home' /var/log/exim_mainlog | awk '{print $3}' | cut -d / -f 3 | sort -bg | uniq -c | sort -bg
}}}
You will get a result as like follows for the first two scripts. The third script is just a sub of the first two scripts.

{{{
9               cwd=/home/test1/public_html
10             cwd=/home/test2/public_html/a1/www
15             cwd=/home/test3/public_html
91             cwd=/home/test4/public_html
178           cwd=/home/test5/public_html/web
770           cwd=/home/test6/public_html/foro
803           cwd=/home/test7/public_html/web
124348      cwd=/home/test8/public_html/wp/wp-content/themes/twentyeleven
}}}
2- If we need to find out exact spamming script. The following script will shows the current spamming script running now. The following script will help you in all time of mail servers. It will help you to find the exact script which sending mails.

{{{
# ps auxwwwe | grep <user> | grep --color=always "<location of script>" | head
}}}
The usage of the above script is as shown below.

{{{
# ps auxwwwe | grep test8 | grep --color=always "/home/test8/public_html/wp/wp-content/themes/twentyeleven" | head
}}}
Once you find the exact script, the following script will help you to find the IP address which is responsible for spamming. You will get a list of IPs from the following script. The IPs address which has high number of access is most probably causing spamming. You can block the IP address in csf or apf firewall.

{{{
# grep "<script_name>" /home/user/access-logs/testdomain.com | awk '{print $1}' | sort -n | uniq -c | sort -n
}}}
Following command that will show you the script which is using script to send the email. If it is from php then use

{{{
# egrep -R "X-PHP-Script"  /var/spool/exim/input/*
}}}
It shows top 50 domains using mail server with options.

{{{
# eximstats -ne -nr /var/log/exim_mainlog
}}}
It shows from which user’s home the mail is going, so that you can easily trace it and block it if needed.it shows the mails going from the server.

{{{
# ps -C exim -fH ewww | grep home
}}}
It shows the IPs which are connected to server through port number 25. It one particular Ip is using more than 10 connection you can block it in the server firewall.

{{{
# netstat -plan | grep :25 | awk {'print $5'} | cut -d: -f 1 | sort | uniq -c | sort -nk 1
}}}

{{{
cat /var/log/exim_mainlog | grep "connection count = " | tail
grep 41.187.100.20 /var/log/exim_mainlog | grep 'connection count' | tail
}}}

In order to find “nobody” spamming, issue the following command

{{{
# ps -C exim -fH ewww | awk '{for(i=1;i<=40;i++){print $i}}' | sort | uniq -c | grep PWD | sort -n
}}}
It will give some result like: Example :

{{{
6 PWD=/
347 PWD=/home/sample/public_html/test
}}}
Count the PWD and if it is a large value check the files in the directory listed in PWD (Ignore if it is / or /var/spool/mail /var/spool/exim)

The above command is valid only if the spamming is currently in progress. If the spamming has happened some hours before, use the following command.

{{{
# grep "cwd=" /var/log/exim_mainlog | awk '{for(i=1;i<=10;i++){print $i}}' | sort | uniq -c | grep cwd | sort -n
}}}
The following script will give the summary of mails in the mail queue.

{{{
exim -bpr | exiqsumm -c | head
}}}
You will get a result as like follows,

{{{
Count  Volume  Oldest  Newest  Domain
-----      ------      ------     ------       ------

114    171KB     24h     28m  testdomain.com
15      28KB       36h     7m    gmail.com
5        10KB       34h     10h   test2domain.com
4        8192         27h     4h     yourdomain.com
4        75KB        7m      7m    server.domain.com
3        6041         23h     42m  test123.com
}}}
Script to delete all mails in exim Queue

{{{
exim -bp | awk '/^ *[0-9]+[mhd]/{print "exim -Mrm " $3}' | bash
}}}
== Spammer notification Script ==
1- Create script (spammer.sh)

 .{{{
#!/bin/bash

spammer=`ps -C exim -fH ewww | grep home`
if [ "$spammer" != "" ] ;then

echo "Dear Team," >> /tmp/spammmer
echo "        " >> /tmp/spammmer
echo "Kindly find cpanel.noorhosting.com spammer report" >> /tmp/spammmer
echo "        " >> /tmp/spammmer

grep "cwd=/home" /var/log/exim_mainlog | awk '{for(i=1;i<=10;i++){print $i}}' | sort | uniq -c | grep cwd | sort -n >> /tmp/spammmer 2>&1

echo "        " >> /tmp/spammmer

ps -C exim -fH ewww | grep home >> /tmp/spammmer 2>&1

echo "        " >> /tmp/spammmer

grep "Script Alert" /var/log/exim_mainlog >> /tmp/spammmer 2>&1

echo "Thanks" >> /tmp/spammmer
echo "Hosting Team" >> /tmp/spammmer

sleep 2

mail -s "cPanel Spammer Report"  logs-sys@noor.net < /tmp/spammmer

rm -rf /tmp/spammmer

fi
}}}

2- Make it excutable

{{{
chmod a+x spammer.sh
}}}
3- Schedule it every 15 minutes

{{{
#check spammer
*/15 * * * * /usr/bin/spammer.sh
}}}

== Domains Exceeded Mail Limit notification Script ==
1- Create script (spammer2.sh)

 .{{{
#!/bin/bash
spamdate=`date --iso`
spammer2=`cat /var/log/exim_mainlog | grep $spamdate | grep "has exceeded the max defers and failures per hour"`
if [ "$spammer2" != "" ] ;then

echo "Dear Team," >> /tmp/spammmer2

echo "        " >> /tmp/spammmer2

echo "Kindly find cpanel.noorhosting.com domains which have exceeded mail limit" >> /tmp/spammmer2
echo "        " >> /tmp/spammmer2

echo " ###### Domains exceeded the max defers and failures per hour ######  " >> /tmp/spammmer2

echo "        " >> /tmp/spammmer2

cat /var/log/exim_mainlog | grep $spamdate | grep "has exceeded the max defers and failures per hour" >> /tmp/spammmer2 2>&1

echo "        " >> /tmp/spammmer2

echo " ########## Domains exceeded the max emails per hour ############  " >> /tmp/spammmer2

echo "        " >> /tmp/spammmer2

cat /var/log/exim_mainlog | grep "has exceeded the max emails per hour" >> /tmp/spammmer2 2>&1

echo "        " >> /tmp/spammmer2


echo "Thanks" >> /tmp/spammmer2
echo "Hosting Team" >> /tmp/spammmer2

sleep 2

mail -s "cPanel Domains Exceeded mail Limit Report"  logs-sys@noor.net < /tmp/spammmer2

rm -rf /tmp/spammmer2

fi
}}}

2- Make it excutable

{{{
chmod a+x spammer2.sh
}}}
3- Schedule it every hour

{{{
#check domain mail limit
0 * * * * /usr/bin/spammer2.sh
}}}


== EGYPT Blocked and Failed IPs notification Script ==

1- Create script (spammer3.sh)

 .{{{
#!/bin/bash
spamdate=`date +%b" "%e`
spammer3=`cat /var/log/lfd.log| grep "$spamdate" | grep "(EG/Egypt/-)"`

#spammer3=`cat /var/log/lfd.log | grep "(EG/Egypt/-)"`

if [ "$spammer3" != "" ] ;then 

echo "Dear Team," >> /tmp/spammmer3

echo "        " >> /tmp/spammmer3

echo "Kindly find cpanel.noorhosting.com Egypt Login Falied & Blocked IPs" >> /tmp/spammmer3
echo "        " >> /tmp/spammmer3

echo "        " >> /tmp/spammmer3

#cat /var/log/lfd.log | grep "(EG/Egypt/-)" >> /tmp/spammmer3 2>&1

cat /var/log/lfd.log | grep "(EG/Egypt/-)" | grep "$spamdate"  >> /tmp/spammmer3 2>&1

echo "        " >> /tmp/spammmer3


echo "Thanks" >> /tmp/spammmer3
echo "Hosting Team" >> /tmp/spammmer3

sleep 2

mail -s "cPanel Egypt Login failed & Blocked IPs"  logs-sys@noor.net < /tmp/spammmer3

rm -rf /tmp/spammmer3

fi


}}}

2- Make it excutable

{{{
chmod a+x spammer3.sh
}}}
3- Schedule it dailt at 7am

{{{
#EGYPT Blocked IPs
0 7 * * * /usr/bin/spammer3.sh
}}}
