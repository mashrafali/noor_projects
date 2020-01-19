{{{
Author       : Mohamed Ashraf
Title        : Deploying SMTP Filter Cluster with Load Balancing and Nodes status check
Version      : 1.0
Creation date: 3/12/2014
Latest update: 3/12/2014
}}}



<<TableOfContents(4)>>



{{attachment:SpamAssassin.png}}




= Introduction =

In this Project We will build and deploy a Filter Cluster that operates on SMTP requests, with High Availability Options and Load Balancing between BackEnd Nodes.

The Open Source Tools that is going to be used in this project is:

1- Scroll out F1 E-Mail Gateway Project as the Filter Engine.

2- HAProxy as the Load Balancer Engine.

3- Keepalived as the High Availability application.

4- Apache as a web-server

5- Bind9 as the DNS Server


= Topology =

The Topology Design will be as follows:

{{attachment:Cluster-Design.jpg}}


= The Filter Engine =

As Illustrated in the Desgin, we will build 3 Nodes dedicated for Filter Handling.

The number for Nodes depends on the Estimated expected Load, and the design itself is versatile where more nodes can be commissioned at any time with minimum configuration.

For the Filter Engine itself - per node - we will install and deploy '''Scroll out F1 SMTP Filter'''

== Installation ==

Two methods of installation can be used:

=== Installation Method 1 ===

- Install the latest ISO Bootable CD from the website:

{{{
http://sourceforge.net/projects/scrollout/files/latest/download
}}}

- Then Boot from the CD and install the Debian Based System, where all the Filter components are pre-installed for you.

=== Installation Method 2 ===

- Download and install the Latest release of Debian server, take note that the Server needs to be '''CLEAN'''.

- Make sure the server is fully updated before proceeding with the installation, refer to:

{{{
http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/ubuntu-Initialization
}}}


- Apply the Following Commands to start installing the Filter Engine

{{{
sudo –i
cd /tmp
wget http://sourceforge.net/projects/scrollout/files/update/scrolloutf1.tar/download -O scrolloutf1.tar
tar -xvf scrolloutf1.tar
chmod 755 /tmp/scrolloutf1/www/bin/*
/tmp/scrolloutf1/www/bin/install.sh
}}}


== Post Installation ==


- When the installation is done, you can enter the system configuration through the following:

{{{
URL: http(s)://host-ip/ 
User: Admin
Default Password: 123456
}}}

- Make sure that the Following Ports are open in any firewall/policy box configurations for the system to be used:

{{{
out 53 UDP - DNS query, RBL
out 80 TCP - HTTP updates
in 80 TCP - web management access
in 443 TCP - web management access over TLS/SSL (recommended)
in 25/587 TCP - incoming SMTP traffic (+ other customized local ports)
out 25 TCP - outgoing SMTP traffic (+ other remote ports)
in/out 8080 TCP - proxy web cache for intranets
out 6277 UDP - DCC service
out 2703 TCP - Razor2 service
out 24441 UDP - Pyzor service
out 123 UDP - NTP time sync
in/out 4500 UDP - IPSec NAT Traversal
}}}

== Building a Domain Logger ==

- It is most certainly needed to build Loggers per each domain, so we can trace events per domain

to do so, we will apply below steps:

=== Creating the Logger Worker ===

- To build the worker, the following Script can be used

'''@/usr/bin/domain-logger'''

{{{
#! /bin/bash

#Revising Reporting
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /etc/postfix/sender
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/index.php 
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/1/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/2/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/3/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/4/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/5/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/6/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/7/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/8/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/9/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/cfg/agresivity/10/20-debian_defaults
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/index.html
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/tooltips/domain
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/tooltips/mailbox
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/tooltips/legit_folder
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/tooltips/imap_server
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/tooltips/spam_folder
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/tooltips/collector
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/tooltips/collector
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/bin/notify.sh
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/bin/uninstall.sh
sed -i 's/Scrollout F1/NOOR Filter Cluster/g' /var/www/codes.cfg

#Domain Logging initiation
(tail -f /var/log/mail.log | grep -i "@noorgroup.net"              >> /root/domains-logs/noorgroup.net &)
(tail -f /var/log/mail.log | grep -i "@noor.net"                   >> /root/domains-logs/noor.net &)
(tail -f /var/log/mail.log | grep -i "@mdc-egypt.org"              >> /root/domains-logs/mdc-egypt.org &)
(tail -f /var/log/mail.log | grep -i "@freightsolutions.com.eg"    >> /root/domains-logs/freightsolutions.com.eg &)
(tail -f /var/log/mail.log | grep -i "@lecico.com.eg"              >> /root/domains-logs/lecico.com.eg &)
(tail -f /var/log/mail.log | grep -i "@lecico.com"                 >> /root/domains-logs/lecico.com &)
(tail -f /var/log/mail.log | grep -i "@lecico.co.uk"               >> /root/domains-logs/lecico.co.uk &)
(tail -f /var/log/mail.log | grep -i "@Sarrdesign.com"             >> /root/domains-logs/Sarrdesign.com &)
(tail -f /var/log/mail.log | grep -i "@sarregypt.com"              >> /root/domains-logs/sarregypt.com &)
(tail -f /var/log/mail.log | grep -i "@sar-san.com"                >> /root/domains-logs/sar-san.com &)
(tail -f /var/log/mail.log | grep -i "@gtegypt.org"                >> /root/domains-logs/gtegypt.org &)
(tail -f /var/log/mail.log | grep -i "@haitham.com"                >> /root/domains-logs/haitham.com &)


#Client reporting activtion
(tail -f /var/log/mail.log | grep -i "@lecico.com.eg"             | grep -i NOQUEUE     >> /root/server-status/CLIENT-REPORTING &)
(tail -f /var/log/mail.log | grep -i "@freightsolutions.com.eg"   | grep -i NOQUEUE     >> /root/server-status/CLIENT-REPORTING &)
(tail -f /var/log/mail.log | grep -i "@mdc-egypt.org"             | grep -i NOQUEUE     >> /root/server-status/CLIENT-REPORTING &)
(tail -f /var/log/mail.log | grep -i "@gtegypt.org"               | grep -i NOQUEUE     >> /root/server-status/CLIENT-REPORTING &)
}}}

- Do not forget to make the Script Executable:

{{{
chmod a+x /usr/bin/domain-logger
}}}

=== Creating the Logger Daemon ===

- Since we need the loggers to be always up with the system, we will create a daemon for it

to do so, create the following

'''@/etc/init.d/domain-logger'''

Parse with '''''/bin/sh -e'''''

{{{
#!/bin/sh -e
# Start or stop NOOR-SCAN Domain Logger


# By Mohamed Ashraf



### BEGIN INIT INFO


# Provides:          NOOR-SCAN (Domain Logger)

# Required-Start:    tail & mail.log of postfix

# Required-Stop:     tail & mail.log of postfix

# Short-Description: Start NSCAN DOMAIN LOGGER

# Description:       Enable service provided by daemon.


### END INIT INFO
PATH=/bin:/usr/bin:/sbin:/usr/sbin
case "$1" in
start)
echo
echo -n "Starting NOOR-SCAN DOMAIN LOGGERS.."

cd /usr/bin/
./domain-logger
sleep 2
echo "Loggers Activated!"
echo
sleep 1
;;

stop)
echo
echo -n "Stopping NOOR-SCAN DOMAIN LOGGERS.."
sleep 2
pkill tail -f
echo "Loggers Stopped!"
echo
sleep 1
;;

restart)
$0 stop || true
$0 start
;;
*)
echo "Usage: /etc/init.d/domain-logger {start|stop|restart}"


exit 1


;;
esac
exit 0
}}}

- Make The Script Executable

{{{
chmod a+x /etc/init.d/domain-logger
}}}

- Then initialize the Script to be run by the kernel at system Boot

{{{
update-rc.d load-balancer defaults
}}}

- Start the ''domain-loggers'' Daemon.

{{{
/etc/init.d/domain-logger start
}}}

== Useful Scripts ==


- The following Useful Scripts can be used on the Cluster Nodes.


=== Flushing Certain Domain ===

- When a Certain Domain fills the queue and needs to be flushed, the following Script can be used:

Parse with '''/usr/bin/perl''' :

{{{
#!/usr/bin/perl
 
$REGEXP = shift || die "no email-adress given (regexp-style, e.g. bl.*\@yahoo.com)!";
 
@data = qx</usr/sbin/postqueue -p>;
for (@data) {
  if (/^(\w+)(\*|\!)?\s/) {
     $queue_id = $1;
  }
  if($queue_id) {
    if (/$REGEXP/i) {
      $Q{$queue_id} = 1;
      $queue_id = "";
    }
  }
}
 
#open(POSTSUPER,"|cat") || die "couldn't open postsuper" ;
open(POSTSUPER,"|postsuper -d -") || die "couldn't open postsuper" ;
 
foreach (keys %Q) {
  print POSTSUPER "$_\n";
};
close(POSTSUPER);
 
}}}

- Usage :

{{{
Assuming you named the file "flush-this-domain"
then:
./flush-this-domain <Domain Name>
}}}

=== Hourly Queue Report ===

- If for debugging reasons an hourly mail queue report is needed, the following script can be used:

{{{
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
result=$(mailq | grep Requests | cut -d" " -f5)

if [ -n "$result" ]
  then
   result=$(mailq | grep Requests | cut -d" " -f5)
  else
   result=Zero
fi

echo "-- TOTAL NUMBER OF MAILS IN QUEUE : $result" >> queue-body.txt 
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
echo "############################################### Blocked Gmail Mails ##########################################" >> queue-body.txt
cat /root/domains-logs/blocked-gmail.com >> queue-body.txt



sendmail hhh@noor.net < queue-body.txt


#mailx -s "[NOOR SCAN] Hourly Queue Report" -t mashraf@noor.net hhh@noor.net < queue-body.txt
#mailx -s "[NOOR SCAN] Hourly Queue Report" -t mashraf@noor.net < queue-body.txt
}}}

- Then it can be added to Crontab to EXE at hourly basis.


=== Block Reporting ===

- If the server is needed to send why it blocked certain messages at first SMTP Handshakes [Pre-Quarantine], with the Ability to Notify Certain Domain Admins if needed;

The following Script Can be used

{{{
#! /bin/bash


#match=$(tail -1 CLIENT-REPORTING)

cd /root/server-status/
##### Codes Conditioning phase:

while read line
do

match=$line

#Setting Reset paramteres IF NO-CODE or Unmatched NOQUEUE

code="unmatched"
sender="Could not Extract or unmatched code"
recipient="Could not Extract or unmatched code"
extract="Reason Unknown"
senderMX="Unspecified"
message="There is Nothing to say!"
domain="Unfortunatly....i dont know."
send="ok"


# IF Code [450 4.1.1]                                                         http://www.postfix.org/ADDRESS_VERIFICATION_README.html
if [[ $match == *450\ 4.1.1* ]]
then
  code="450 4.1.1"
  sender=$(echo $match | awk -v FS="(from=<|>)" '{print $3}')
  senderMX=$(echo $match | awk -v FS="(RCPT from |: )" '{print $5}')
  recipient=$(echo $match | awk -v FS="(to=<|> )" '{print $3}')
  extract=$(echo $match | awk -v FS="(>: |; )" '{print $2}')                            #Sender Address Rejected
  message="suspicious recipient detected, Verifying recipient with his mail server before sending this mail."
  send="ok"
fi

# IF Code [450 4.1.8]
if [[ $match == *450\ 4.1.8* ]]
then
  code="450 4.1.8"
  sender=$(echo $match | awk -v FS="(from=<|>)" '{print $3}')
  senderMX=$(echo $match | awk -v FS="(RCPT from |: )" '{print $5}')
  recipient=$(echo $match | awk -v FS="(to=<|> )" '{print $3}')
  extract=$(echo $match | awk -v FS="(>: |: D)" '{print $2}')                            #Sender Address Rejected
  message="$extract, His Domain Name could not be resolved/found or is fake."
  send="ok"
fi

# IF Code [550 5.1.1]
if [[ $match == *550\ 5.1.1* ]]
then
  code="550 5.1.1"
  sender=$(echo $match | awk -v FS="(from=<|>)" '{print $3}')
  senderMX=$(echo $match | awk -v FS="(RCPT from |: )" '{print $5}')
  recipient=$(echo $match | awk -v FS="(to=<|> )" '{print $3}')
  extract="No mailbox here by that name"                                                 #Always same message, no need for parsing extraction
  message="Recieving Mail server Said [$extract]."
  send="ok"
fi


# IF Code [550 5.5.1]                                                                    http://sourceforge.net/p/scrollout/discussion/1102835/thread/2db59db8/
if [[ $match == *550\ 5.5.1* ]]
then
  code="550 5.5.1"
  sender=$(echo $match | awk -v FS="(from=<|>)" '{print $2}')
  senderMX=$(echo $match | awk -v FS="(RCPT from |: )" '{print $5}')
  recipient=$(echo $match | awk -v FS="(to=<|>, )" '{print $3}')
  extract="SMTP Protocol Error"                                                           #Always same message, no need for parsing extraction
  message="Bot Behaviour Detected during SMTP communication."
  send="ok"
fi


# IF Code [550 5.7.1]
if [[ $match == *550\ 5.7.1* ]]
then
  code="550 5.7.1"
  sender=$(echo $match | awk -v FS="(from=<|>)" '{print $2}')
  senderMX=$(echo $match | awk -v FS="(RCPT from |: )" '{print $5}')
  recipient=$(echo $match | awk -v FS="(to=<|>, )" '{print $3}')
  extract=$(echo $match | awk -v FS="(blocked using |; )" '{print $3}')      #some.dnsbl.net
  message="Sender IP is blacklisted @[$extract], All communications is refused from him."
  send="ok"
fi


if [ -z "$sender" ]
then
  sender="Sender MX Server did not specify a sender Address"
fi
if [ -z "$recipient" ]
then
  recipient="Not Specified"
fi



######## Domain Extraction phase:

shopt -s nocasematch #drop case-sensitive comparison

if [[ "$match" =~ "lecico.com" ]]; then
  domain="lecico.com"
fi

if [[ "$match" =~ "lecico.com.eg" ]]; then
  domain="lecico.com.eg"
fi

if [[ "$match" =~ "freightsolutions.com.eg" ]]; then
  domain="freightsolutions.com.eg"
fi

if [[ "$match" =~ "mdc-egypt.org" ]]; then
  domain="mdc-egypt.org"
fi

if [[ "$match" =~ "gtegypt.org" ]]; then
  domain="gtegypt.org"
fi

shopt -u nocasematch #Re-gain case-sensitive knowledge


####### Reporting Phase
echo "SUBJECT: [Filter Cluster Node1] Experimental Report Testing meant for [$domain]"                                                              > block-report.msg
echo "FROM: Somone-out-there@his.pc "                                                                                                          >> block-report.msg
echo "TO: Targeted.person@some-domain.otr "                                                                                                    >> block-report.msg
echo "CC: maybeNoor@noordomain.net "                                                                                                           >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo " =====  Test check Begin  ====="                                                                                                         >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo "Extracted Parameters:"                                                                                                                   >> block-report.msg
echo "---------------------"                                                                                                                   >> block-report.msg
echo Domain    : $domain                                                                                                                       >> block-report.msg
echo Code      : $code                                                                                                                         >> block-report.msg
echo Sender    : $sender                                                                                                                       >> block-report.msg
echo Sender MX : $senderMX                                                                                                                     >> block-report.msg
echo Recipient : $recipient                                                                                                                    >> block-report.msg
echo Extract   : $extract                                                                                                                      >> block-report.msg
echo Message   : $message                                                                                                                      >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo " =====  Test Check End  ====="                                                                                                           >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo " ===== Example Message to be sent Begin  ====="                                                                                          >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo "Dear Valued Customer,"                                                                                                                   >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo "NOOR's SMTP Filter has intercepted a suspecious E-Mail sent to your domain, and would like to share with you the following report:"      >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo "- The Mail was sent from      : $senderMX   [The IP Adress of the mail server this mail was recieved from] "                             >> block-report.msg
echo "- Used Sender Address in mail : $sender   [The Person who Claims to be the sender of the intercepted mail] "                             >> block-report.msg
echo "- The mail was directed to    : $recipient   [The Person who was going to recieve the intercepted mail] "                                >> block-report.msg
echo "- Reason of delivery block    : $message "                                                                                               >> block-report.msg
echo "- SMTP Error Code             : $code    "                                                                                               >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo "Kindly find below the intercepted mail log found within our Filter Servers:"                                                             >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo $match                                                                                                                                    >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo "* If you think that this message should not be blocked, kindly contact us at [it@noor.net]."                                             >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo "Best regards,"                                                                                                                           >> block-report.msg
echo "Signature and stuff "                                                                                                                    >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo " ===== Example Message to be sent END  ====="                                                                                            >> block-report.msg
echo                                                                                                                                           >> block-report.msg
echo                                                                                                                                           >> block-report.msg
#############
#Sending the report

if [ $send == "ok" ]; then
    sendmail hhh@noor.net < block-report.msg
fi

#Parsing Test Parameters, Activate only in debugging:

#echo Domain : $domain
#echo Code: $code
#echo Sender: $sender
#echo Sender MX: $senderMX
#echo Recipient: $recipient
#echo Extract: $extract
#echo Message: $message
#echo "-------------------------------------------------"
#echo

done < CLIENT-REPORTING


#Clearing Sent Reports

echo -n "" > CLIENT-REPORTING
}}}

- Can Be added to Crontab for minute by minute check.

=== Node Status Reporting ===

- If it is needed for the Node to send its Status for monitoring purposes for example every 24 hours, the following script can be used:

{{{
#! /bin/bash


## Prepare Data
Q1=$(cat /root/domains-logs/noorgroup.net | grep -i quarantine | wc -l)
R1=$(cat /root/domains-logs/noorgroup.net | grep -i NOQUEUE | wc -l)
V1=$(cat /root/domains-logs/noorgroup.net | grep -i infected | wc -l)

Q2=$(cat /root/domains-logs/freightsolutions.com.eg | grep -i quarantine | wc -l)
R2=$(cat /root/domains-logs/freightsolutions.com.eg | grep -i NOQUEUE | wc -l)
V2=$(cat /root/domains-logs/freightsolutions.com.eg | grep -i infected | wc -l)

Q3=$(cat /root/domains-logs/mdc-egypt.org | grep -i quarantine | wc -l)
R3=$(cat /root/domains-logs/mdc-egypt.org | grep -i NOQUEUE | wc -l)
V3=$(cat /root/domains-logs/mdc-egypt.org | grep -i infected | wc -l)

Q4=$(cat /root/domains-logs/lecico.com.eg | grep -i quarantine | wc -l)
R4=$(cat /root/domains-logs/lecico.com.eg | grep -i NOQUEUE | wc -l)
V4=$(cat /root/domains-logs/lecico.com.eg | grep -i infected | wc -l)

Q5=$(cat /root/domains-logs/lecico.com | grep -i quarantine | wc -l)
R5=$(cat /root/domains-logs/lecico.com | grep -i NOQUEUE | wc -l)
V5=$(cat /root/domains-logs/lecico.com | grep -i infected | wc -l)

Q6=$(cat /root/domains-logs/lecico.co.uk | grep -i quarantine | wc -l)
R6=$(cat /root/domains-logs/lecico.co.uk | grep -i NOQUEUE | wc -l)
V6=$(cat /root/domains-logs/lecico.co.uk | grep -i infected | wc -l)

Q7=$(cat /root/domains-logs/Sarrdesign.com | grep -i quarantine | wc -l)
R7=$(cat /root/domains-logs/Sarrdesign.com | grep -i NOQUEUE | wc -l)
V7=$(cat /root/domains-logs/Sarrdesign.com | grep -i infected | wc -l)

Q8=$(cat /root/domains-logs/sarregypt.com | grep -i quarantine | wc -l)
R8=$(cat /root/domains-logs/sarregypt.com | grep -i NOQUEUE | wc -l)
V8=$(cat /root/domains-logs/sarregypt.com | grep -i infected | wc -l)

Q9=$(cat /root/domains-logs/sar-san.com | grep -i quarantine | wc -l)
R9=$(cat /root/domains-logs/sar-san.com | grep -i NOQUEUE | wc -l)
V9=$(cat /root/domains-logs/sar-san.com | grep -i infected | wc -l)

Q10=$(cat /root/domains-logs/gtegypt.org | grep -i quarantine | wc -l)
R10=$(cat /root/domains-logs/gtegypt.org | grep -i NOQUEUE | wc -l)
V10=$(cat /root/domains-logs/gtegypt.org | grep -i infected | wc -l)

Q11=$(cat /root/domains-logs/haitham.com | grep -i quarantine | wc -l)
R11=$(cat /root/domains-logs/haitham.com | grep -i NOQUEUE | wc -l)
V11=$(cat /root/domains-logs/haitham.com | grep -i infected | wc -l)


## Building Report

cd /root/server-status
echo "SUBJECT: [Filter Cluster Node 1] Server Status Report" > body.txt
echo "TO: mashraf@noor.net " >> body.txt
echo "TO: hhh@noor.net " >> body.txt
echo "CC: " >> body.txt
echo >> body.txt

echo "                                      NOOR-SCAN SERVER STATUS" >> body.txt
echo >> body.txt
echo $(date +"%A %d %B %Y [%r]") >> body.txt
echo >> body.txt
echo >> body.txt

result=$(mailq | grep Requests | cut -d" " -f5)

if [ -n "$result" ]
  then
   result=$(mailq | grep Requests | cut -d" " -f5)
  else
   result=Zero
fi

echo "-- TOTAL NUMBER OF MAILS IN QUEUE : $result" >> body.txt 
echo  >> body.txt
echo  >> body.txt

echo "Memory:" >> body.txt
echo "-------" >> body.txt
free -h >> body.txt
echo >> body.txt
echo >> body.txt

echo "Disk Space:" >> body.txt 
echo "-----------" >> body.txt 
pydf --bw >> body.txt
echo >> body.txt
echo >> body.txt

echo "CPU Cores:" >> body.txt 
echo "----------" >> body.txt 
mpstat -P ALL >> body.txt
echo >> body.txt
echo >> body.txt


echo "Domain-Name                  Quarantines                  Rejected                  Virus" > format.txt 
echo "-----------                  -----------                 --------                  -----" >> format.txt 
echo -n "noorgroup.net           " >> format.txt && echo "  $Q1  $R1  $V1"     >> format.txt
echo -n "freightsolutions.com.eg " >> format.txt && echo "  $Q2  $R2  $V2"     >> format.txt
echo -n "mdc-egypt.org           " >> format.txt && echo "  $Q3  $R3  $V3"     >> format.txt
echo -n "lecico.com.eg           " >> format.txt && echo "  $Q4  $R4  $V4"     >> format.txt
echo -n "lecico.com              " >> format.txt && echo "  $Q5  $R5  $V5"     >> format.txt
echo -n "lecico.co.uk            " >> format.txt && echo "  $Q6  $R6  $V6"     >> format.txt
echo -n "Sarrdesign.com          " >> format.txt && echo "  $Q7  $R7  $V7"     >> format.txt
echo -n "sarregypt.com           " >> format.txt && echo "  $Q8  $R8  $V8"     >> format.txt
echo -n "sar-san.com             " >> format.txt && echo "  $Q9  $R9  $V9"     >> format.txt
echo -n "gtegypt.org             " >> format.txt && echo "  $Q10  $R10  $V10"  >> format.txt
echo -n "haitham.com             " >> format.txt && echo "  $Q11  $R11  $V11"  >> format.txt

column -t -s ' ' format.txt >> body.txt
echo >> body.txt
echo >> body.txt



#echo "-- TOTAL NUMBER OF MAILS IN QUEUE : $(mailq | grep Requests | cut -d" " -f5)" >> body.txt
#echo "Qeued Mails:" >> body.txt 
#echo "------------" >> body.txt 
#mailq >> body.txt
#echo >> body.txt
#echo >> body.txt


sendmail hhh@noor.net < body.txt

#mailx -s "[NOOR SCAN] Server Status Report" -t mashraf@noor.net hhh@noor.net < body.txt
#mailx -s "[NOOR SCAN] Server Status Report" -t mashraf@noor.net < body.txt
}}}

- Then it can be added to crontab to EXE at whatever Cycle we need. i.e: every 24 hrs.


= TITAN HUB =

- The Cluster Gateway, is as per Design responsible for passing all SMTP Requests to and from the BackEnd Nodes.

- It has many other Functions for The cluster which summarizes as below:

1- Load Balancer Box between the Cluster Nodes.

2- DNS Server for the Cluster, so that all Nodes know one another and their Cluster GW.

3- Natter Box for the Cluster, where all cluster traffic passes through it, and to ensure that the Cluster nodes are secured and untouched from outside.

4- HTTP Redirector to the Cluster Main-Node, so that we can manage all Cluster nodes with a single IP & Interface.

5- A Log Collector Box For All the Nodes in the Cluster, for a Centralized View.

6- Organizes the Configuration Duplication between the Main-Node and all the Other Slave-Nodes in the Cluster.

- And that is why we will code-name it '''''TITAN HUB'''''.

- Lets begin with Building the Box:

== Installation ==

- Start from the beginning, where you download and install a '''CLEAN''' Ubuntu server, Ubuntu 1404 LTS was used for this task.


- Initialize the server before use, refer to

{{{
http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/ubuntu-Initialization
}}}

=== Building the Balancer ===

- You can then start by building the Load Balancer Module on the box, refer to:

{{{
http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/load-balancer
}}}

- In our case, the SMTP Filter Engine needs to know the source IP of the SMTP Request itself.

- For this Reason the following Configuration Parameters have been used for the Balancer

{{{
global
        log 127.0.0.1   local0
        log 127.0.0.1   local1 notice
        maxconn 6000
        chroot /var/lib/haproxy
        user root
        group root
        daemon
#       debug
#       quiet

frontend FRONT-FACING
        bind 0.0.0.0:25 transparent
        mode tcp
        no option http-server-close
        timeout client 1m
        log global
        option tcplog
        default_backend TO-FILTER-CLUSTER

backend TO-FILTER-CLUSTER
        source 0.0.0.0 usesrc clientip
        mode tcp
        no option http-server-close
        log global
        option tcplog
#       option smtpchk HELO noor.net
        timeout server 1m
        timeout connect 5s
        balance roundrobin

        server filter1 10.10.10.1:25 send-proxy weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
        server filter2 10.10.10.2:25 send-proxy weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
        server filter3 10.10.10.3:25 send-proxy weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
}}}

- Do '''NOT''' Forget to add the below to your '''/etc/sysctl.conf''', to allow Transparent Proxy:

{{{
##Added for TPROXY#####                
net.ipv4.ip_forward = 1
kernel.sem = 250 32000 100 128
kernel.shmmax = 536870912
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.eth0.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.lo.send_redirects = 0
}}}

- And there you go, you have a working Load Balancer Module installed and ready to go for your cluster.

- You can make sure that the Balancer is working in transparent mode by checking source IPs recieved on your nodes, such info can be found @'''/var/log/mail.log'''


=== Introduce everyone: DNS ===

- For every Node to be Aware of its TITAN Hub and be able to query safely and controllably within the Cluster, A DNS server will be built on the Gateway.

- Bind9 will be used for this task, as below:

{{{
apt-get update && apt-get install bind9 -y
}}}

- Apply the following Configuration to allow Recursion for the Cluster Nodes:

'''@/etc/bind/named.conf.options'''

{{{
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================


        //dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };

        allow-recursion { localhost; 10.10.10.0/24;};
        dnssec-validation no;
};
}}}

- You can add a zone named for example "smtp-cluster" and proceed to add DNS records for them.

- You can also Optionally add the nodes within the '''/etc/hosts''' file of the TITAN.

{{{
127.0.0.1       localhost.localdomain localhost
192.168.0.14    titan.noor.net titan
10.10.10.1      main-node-1    node1
10.10.10.2      slave-node-2   node2
10.10.10.3      slave-node-3   node3
10.10.10.10     titan.noor.net titan

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
}}}


- To make it even more awesome, we will proceed to configure SSH Keys between TITAN and it's nodes, so that you flexibly transfer between them using TITAN's root privileges.

Apply for every node:

First Create your keys:

{{{
ssh-keygen -t rsa
}}}

- Just hit enter and leave default values for all

The process should look something like this:

{{{
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 root@titan
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
}}}

- Then you can proceed to transfer your key to the Cluster nodes

{{{
ssh-copy-id root@node1
ssh-copy-id root@node2
ssh-copy-id root@node3
}}}

- Notice that we defined "node1,node2,.." either in TITAN's DNS or in TITAN's hosts file.


- After this process is complete, you can add some spices by creating an aliases for SSH!


'''@/root/.bashrc'''

{{{

alias node1='ssh root@node1'
alias node2='ssh root@node2'      
alias node3='ssh root@node3'
}}}

- After this is done, either restart your session or preferably restart TITAN.

- You can now ssh to node 1 by simply typing its name:

{{{
root@TITAN-HUB:~# node1                                      <---- at TITAN

Linux FC-M-node1 3.2.0-4-amd64 #1 SMP Debian 3.2.63-2 x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Dec  3 10:35:48 2014 from 10.10.10.11

root@FC-M-node1:~#                                         <--- Without password
}}}

- Now your TITAN knows its nodes and can secure shell them, without any prompts.


=== Nodes Within a Balloon: Natter Module ===

- Since your Nodes Subnet is isolated within the Cluster, it is TITAN's Duty to transfer traffic to and from the cluster.

- For this reason, we will install a Nating Module on TITAN.

- To Install the module, refer to:

{{{
http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/linux-nat-box
}}}

- After installation is complete, make sure that the BackEnd nodes can normally reach outside.

@NODE1
{{{
root@FC-M-node1:~# ping 8.8.8.8 -c 3
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_req=1 ttl=45 time=72.8 ms
64 bytes from 8.8.8.8: icmp_req=2 ttl=45 time=75.4 ms
64 bytes from 8.8.8.8: icmp_req=3 ttl=45 time=74.8 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 72.873/74.377/75.415/1.133 ms



root@FC-M-node1:~# ping noor.net -c 3
PING noor.net (192.168.0.2) 56(84) bytes of data.
64 bytes from 192.168.0.2: icmp_req=1 ttl=127 time=0.231 ms
64 bytes from 192.168.0.2: icmp_req=2 ttl=127 time=0.371 ms
64 bytes from 192.168.0.2: icmp_req=3 ttl=127 time=0.556 ms

--- noor.net ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 0.231/0.386/0.556/0.133 ms
}}}


- This normally indicates a successful installation of the module.


=== Web Re-Routing: Apache ===

- Since the web interface of the nodes are no longer accessible from outside the cluster - to configure the filter engine - We will adjust TITAN so it redirects http traffic destined to it and passes it the Cluster Main-node.

- For this task we choose APACHE web-server for its wide range of compatible capabilities.

- To build TITAN's Apache, apply the below:

{{{
apt-get update && apt-get install apache2 -y
}}}

- After installation is complete, you need to further activate the modules which will be used later on:

{{{
a2enmod ssl
a2enmod rewrite
a2enmod ssl
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_ajp
a2enmod rewrite
a2enmod deflate
a2enmod headers
a2enmod proxy_balancer
a2enmod proxy_connect
a2enmod proxy_html
a2enmod rewrite
a2enmod ssl
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_ajp
a2enmod rewrite
a2enmod deflate
a2enmod headers
a2enmod proxy_balancer
a2enmod proxy_connect
a2enmod proxy_html
}}}

- Before restarting APACHE Server, Configure the HTTP Directives to redirect to the first node IP with port https

'''@/etc/apache2/sites-available/000-default.conf'''

{{{
<VirtualHost *:80>
        RewriteEngine on
        RewriteCond %{SERVER_PORT} !^443$
        RewriteRule ^/(.*) https://%{HTTP_HOST}/$1
</VirtualHost>

<VirtualHost *:443>
        SSLEngine On
        SSLProxyEngine on
        SSLProxyVerify none
        SSLProxyCheckPeerCN off
        SSLProxyCheckPeerName off
        SSLProxyCheckPeerExpire off
        SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
        SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
        ProxyPreserveHost On

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        RewriteEngine On
        RewriteOptions Inherit
        RewriteRule ^/(.*) https://10.10.10.1/$1 [P]
#       RewriteRule ^node1/(.*)$ https://10.10.10.1/$1 [L,NC,R=301]
</VirtualHost>

<Proxy *>
        Order Deny,Allow
        Deny from all
        Allow from 192.168
</Proxy>
}}}


- Then you can proceed to restart Apache, or preferably restart TITAN.

- This way when you point your browser to TITAN's IP Address, it will instead direct you the configuration of the Main node in the Cluster.


=== Keep Collecting: Log Collector ===

- Since we have several Nodes in the Cluster with Several log files within, it will be difficult to trace all logs.

- For this reason we can Create a LOG Collector Daemon on TITAN, to centralize logs.

- To do so, begin by the following:

1 - Create the Worker:

@/usr/bin/cluster-log.sh
{{{
  /bin/bash     <-- Parser


ssh -n root@node1 'tail -f /var/log/mail.log' >> /root/CLUSTER-LOGS/Cluster-Mail.log &
ssh -n root@node2 'tail -f /var/log/mail.log' >> /root/CLUSTER-LOGS/Cluster-Mail.log &
ssh -n root@node3 'tail -f /var/log/mail.log' >> /root/CLUSTER-LOGS/Cluster-Mail.log &
}}}

- Do not Forget to make it executable

{{{
chmod a+x /usr/bin/cluster-log.sh
}}}

2 - Create the Daemon:

@/etc/init.d/cluster-logs
{{{

  /bin/sh -e      <--- Parser


# Start or stop SMTP FILTER CLUSTER MAIL LOGGING


# By Mohamed Ashraf



### BEGIN INIT INFO


# Provides:          Cluster Mail loggers

# Required-Start:    tail & mail.log of postfix

# Required-Stop:     tail & mail.log of postfix

# Short-Description: Start Cluster Mail Loggers

# Description:       Enable service provided by daemon.


### END INIT INFO
PATH=/bin:/usr/bin:/sbin:/usr/sbin
case "$1" in
start)
echo
echo -n "Starting SMTP FILTER CLUSTER Centralized Logging.."

cd /usr/bin/
./cluster-log.sh
sleep 2
echo "Loggers Activated!"
echo
sleep 1
;;

stop)
echo
echo -n "Stopping SMTP FILTER CLUSTER Centralized Logging.."
sleep 2
pkill tail -f
echo "Loggers Stopped!"
echo
sleep 1
;;

restart)
$0 stop || true
$0 start
;;
*)
echo "Usage: /etc/init.d/domain-logger {start|stop|restart}"


exit 1


;;
esac
exit 0
}}}

- make it executable:

{{{
chmod a+x /etc/init.d/cluster-logs
}}}

- Start the Log-Collector

{{{
/etc/init.d/cluster-logs start
}}}


=== HandShake Everyone: Configuration Sync ===

- For many reasons, the nodes in the Cluster must operate and behave similarly.

- And since it is difficult to configure each node separately, a Configuration Sync between them must be active.

- To apply such idea, the below points have been undertaken

1 - Create the configuration Sync Worker script

'''@/root/CLUSTER-SYNC/cluster-config-sync.sh'''

{{{
 /bin/bash       <--- Parser



## SYNCING CLUSTER CONFIGS
#Script by Mohamed Ashraf


# Locking SELF to Prevent Simultanious Execution
lockfile-create /root/CLUSTER-SYNC/cluster-config-sync.sh

echo
echo
echo "                                       SYNCING CONFIGURATION FILES OVER THE CLUSTER"
echo
echo "                                                                                                     By Mohamed Ashraf"
echo "################################################### PHASE 1 ############ Fetching From Main Node #####################"
echo
sleep 2
echo "-> Fetching configs from Master Node [10.10.10.1]"
echo "-------------------------------------------------"
echo
sleep 2
rsync -azP root@node1:/var/www/ /root/CLUSTER-CONFIGURATION/var-www/ --exclude=connection.cfg --exclude=SYN-LINK
rsync -azP root@node1:/etc/postfix/ /root/CLUSTER-CONFIGURATION/etc-postfix/
rsync -azP root@node1:/etc/spamassassin/ /root/CLUSTER-CONFIGURATION/etc-spamassassin/
# DB Lock Check
if ssh root@node1 "[ -f /var/lib/amavis/.spamassassin/*.lock ]"
then
    echo " ####################################################################### LOCK FILE FOUND FOR Bayesian DataBase!"
    sleep 2
    echo " ####################################################################### !! DB Sync will not continue !!"
    echo
else
    echo " ####################################################################### No Lock File found for DB, Continuing Sync.."
    sleep 1
    echo
    ssh root@node1 'lockfile-create /var/lib/amavis/.spamassassin/bayes_seen'                  # Creating Lock
    ssh root@node1 'lockfile-create /var/lib/amavis/.spamassassin/bayes_toks'                  # Creating Lock
    rsync -azP root@node1:/var/lib/amavis/.spamassassin/ /root/CLUSTER-CONFIGURATION/var-lib-amavis-.spamassassin/ --exclude=*.lock    #Syncing bias DB excpet Lock files
    ssh root@node1 'chown amavis:amavis /var/lib/amavis/.spamassassin/*'                       # Making sure the owner is right
    ssh root@node1 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_seen'                  # RemoVing Lock
    ssh root@node1 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_toks'                  # Removing Lock
fi
echo
echo
echo "################################################### PHASE 2 ############ Feeding The Slave Nodes #####################"
echo
sleep 2
echo "-> Publishing Configuration Over the Cluster Nodes"
echo "--------------------------------------------------"
echo
sleep 2
echo
echo "## Feeding node2 [10.10.10.2]"
echo "-----------------------------"
echo
echo
sleep 2
rsync -azP /root/CLUSTER-CONFIGURATION/var-www/ root@node2:/var/www/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-postfix/ root@node2:/etc/postfix/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-spamassassin/ root@node2:/etc/spamassassin/
# DB Lock Check
if ssh root@node2 "[ -f /var/lib/amavis/.spamassassin/*.lock ]"
then
    echo " ####################################################################### LOCK FILE FOUND FOR Bayesian DataBase!"
    sleep 2
    echo " ####################################################################### !! DB Sync will not continue !!"
    echo
else
    echo " ####################################################################### No Lock File found for DB, Continuing Sync.."
    sleep 1
    echo
    ssh root@node2 'lockfile-create /var/lib/amavis/.spamassassin/bayes_seen'                  # Creating Lock
    ssh root@node2 'lockfile-create /var/lib/amavis/.spamassassin/bayes_toks'                  # Creating Lock
    rsync -azP /root/CLUSTER-CONFIGURATION/var-lib-amavis-.spamassassin/ root@node2:/var/lib/amavis/.spamassassin/      #Syncing bias DB
    ssh root@node2 'chown amavis:amavis /var/lib/amavis/.spamassassin/*'                       # Making sure the owner is right
    ssh root@node2 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_seen'                  # RemoVing Lock
    ssh root@node2 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_toks'                  # Removing Lock
fi
echo
echo
echo "## Feeding node3 [10.10.10.3]"
echo "-----------------------------"
echo
echo
sleep 2
rsync -azP /root/CLUSTER-CONFIGURATION/var-www/ root@node3:/var/www/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-postfix/ root@node3:/etc/postfix/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-spamassassin/ root@node3:/etc/spamassassin/
# DB Lock Check
if ssh root@node3 "[ -f /var/lib/amavis/.spamassassin/*.lock ]"
then
    echo " ####################################################################### LOCK FILE FOUND FOR Bayesian DataBase!"
    sleep 2
    echo " ####################################################################### !! DB Sync will not continue !!"
    echo
else
    echo " ####################################################################### No Lock File found for DB, Continuing Sync.."
    sleep 1
    echo
    ssh root@node3 'lockfile-create /var/lib/amavis/.spamassassin/bayes_seen'                  # Creating Lock
    ssh root@node3 'lockfile-create /var/lib/amavis/.spamassassin/bayes_toks'                  # Creating Lock
    rsync -azP /root/CLUSTER-CONFIGURATION/var-lib-amavis-.spamassassin/ root@node3:/var/lib/amavis/.spamassassin/      #Syncing bias DB
    ssh root@node3 'chown amavis:amavis /var/lib/amavis/.spamassassin/*'                       # Making sure the owner is right
    ssh root@node3 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_seen'                  # RemoVing Lock
    ssh root@node3 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_toks'                  # Removing Lock
fi
echo
echo
echo "################################################### PHASE 3 ############ Restarting Nodes Services ###################"
echo
echo
sleep 2
ssh root@node2 "reboot" > /dev/null
echo
for i in {1..5}
do
echo -en " Slave-Node-2 Realizing New Configuration   -" \\r
sleep 0.25
echo -en " Slave-Node-2 Realizing New Configuration   /" \\r
sleep 0.25
echo -en " Slave-Node-2 Realizing New Configuration   |" \\r
sleep 0.25
echo -en ' Slave-Node-2 Realizing New Configuration   \' \\r
sleep 0.25
done
echo -en ' Slave-Node-2 Realizing New Configuration   OK!' \\r
echo
echo
ssh root@node3 "reboot" > /dev/null
echo
for i in {1..5}
do
echo -en " Slave-Node-3 Realizing New Configuration   -" \\r             
sleep 0.25
echo -en " Slave-Node-3 Realizing New Configuration   /" \\r
sleep 0.25
echo -en " Slave-Node-3 Realizing New Configuration   |" \\r
sleep 0.25
echo -en ' Slave-Node-3 Realizing New Configuration   \' \\r
sleep 0.25
done
echo -en ' Slave-Node-2 Realizing New Configuration   OK!' \\r
echo
echo
echo "                       -----------------> Cluster Syncronization Complete <-----------------       "
echo
echo

# BREAKING SELF LOCK
lockfile-remove /root/CLUSTER-SYNC/cluster-config-sync.sh
}}}

2 - Creating a Trigger at main node:

* The idea here is that every time the "APPLY" button is pressed at the Web-configuration of the main node,

the Sync Script is triggered to transfer the configuration to every other nodes.

'''@/var/www/SYN-LINK'''

{{{
#! /bin/bash

ssh root@10.10.10.10 "/root/CLUSTER-SYNC/cluster-config-sync.sh"
}}}

- Then APPEND the following @'''/var/www/bin/scrollout.sh'''

{{{
## SYNCRONIZE WITH THE CLUSTER
/var/www/SYN-LINK
}}}


- This way we will gain a unified Configuration for all Nodes within the Cluster.

== Useful Scripts ==

- Useful Scripts that can used at TITAN
=== Analyze Daemons ===

/usr/bin/analyze @crontab

{{{
/bin/bash    <---- Precede with "#!"
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin


### FETCHING

check1=$(/etc/init.d/postfix status          | awk {'print $3'})
check2=$(/etc/init.d/clamav-daemon status    | awk {'print $3'})
check3=$(/etc/init.d/clamav-freshclam status | awk {'print $3'})
check4=$(/etc/init.d/amavis status           | awk {'print $3'})
check5=$(/etc/init.d/dovecot status          | awk {'print $3'})
check6=$(/etc/init.d/fail2ban status         | awk {'print $7'})
check7=$(/etc/init.d/incron status           | awk {'print $3'})
check8=$(/etc/init.d/cron status             | awk {'print $3'})
check9=$(/etc/init.d/bind9 status            | awk {'print $3'})
check10=$(/etc/init.d/apache2 status         | awk {'print $3'})
check11=$(/etc/init.d/mailgraph status       | awk {'print $3'})

### CHECKING

let send=0
if [ $check1 == "running." ]
then
  status1="Postfix...................[OK]"
else
  status1="Postfix...................[Failed]"
  let send=$send+1
fi

if [ $check2 == "running." ]
then
  status2="Clamav-daemon.............[OK]"
else
  status2="Clamav-daemon.............[Failed]"
  let send=$send+1
fi

if [ $check3 == "running." ]
then
  status3="Clamav-freshclam..........[OK]"
else
  status3="Clamav-freshclam..........[Failed]"
  let send=$send+1
fi

if [ $check4 == "running." ]
then
  status4="Amavis....................[OK]"
else
  status4="Amavis....................[Failed]"
  let send=$send+1
fi

if [ $check5 == "running." ]
then
  status5="Dovecot...................[OK]"
else
  status5="Dovecot...................[Failed]"
  let send=$send+1
fi

if [ $check6 == "running." ]
then
  status6="Fail2ban..................[OK]"
else
  status6="Fail2ban..................[Failed]"
  let send=$send+1
fi

if [ $check7 == "running." ]
then
  status7="Incron....................[OK]"
else
  status7="Incron....................[Failed]"
  let send=$send+1
fi

if [ $check8 == "running." ]
then
  status8="Cron......................[OK]"
else
  status8="Cron......................[Failed]"
  let send=$send+1
fi

if [ $check9 == "running." ]
then
  status9="Bind......................[OK]"
else
  status9="Bind......................[Failed]"
  let send=$send+1
fi

if [ $check10 == "running" ]
then
  status10="Apache....................[OK]"
else
  status10="Apache....................[Failed]"
  let send=$send+1
fi

if [ $check11 == "running." ]
then
  status11="MailGraph.................[OK]"
else
  status11="MailGraph.................[Failed]"
  let send=$send+1
fi

### Reporting

echo "From: filter@noor.com
Subject: Ramsis SMTP Filter Daemon Alert!

System Date: $(date +%r)

Service Core Daemons Status Changed:

$status1

$status2

$status3

$status4

$status5

$status6

$status7

$status8

$status9

$status10

$status11
" > /tmp/mail-alert

### Sending
touch /tmp/flag
if [ $send != 0 ]
then
    echo "1" > /tmp/flag
    sendmail -t logs-infra@noor.net < /tmp/mail-alert
    sendmail -t logs-sys@noor.net   < /tmp/mail-alert
else
   check=$(cat /tmp/flag)
   if [ "$check" == "1" ]
   then
       sendmail -t logs-infra@noor.net < /tmp/mail-alert
       sendmail -t logs-sys@noor.net   < /tmp/mail-alert
       echo "0" > /tmp/flag
   fi
fi
}}}


=== Cluster Reboot ===

- If for any reason we need to reboot all Cluster Nodes, it will be somewhat time consuming to go to each node and reboot it.

- Instead the below Script can be used for this purpose

'''@/usr/bin/cluster-reboot'''

{{{
 /bin/bash             <--- Parser


clear
echo
echo
echo "              --- >>> REBOOTING SMTP FILTER CLUSTER NODES <<< --- "
echo
echo
sleep 2

ssh root@node1 "reboot" > /dev/null
echo
for i in {1..5}
do
echo -en " Rebooting Main-Node-1    -" \\r
sleep 0.25
echo -en " Rebooting Main-Node-1    /" \\r
sleep 0.25
echo -en " Rebooting Main-Node-1    |" \\r
sleep 0.25
echo -en ' Rebooting Main-Node-1    \' \\r
sleep 0.25
done
echo -en ' Rebooting Main-Node-1    OK!' \\r
echo


ssh root@node2 "reboot" > /dev/null
echo
for i in {1..5}
do
echo -en " Rebooting Slave-Node-2   -" \\r
sleep 0.25
echo -en " Rebooting Slave-Node-2   /" \\r
sleep 0.25
echo -en " Rebooting Slave-Node-2   |" \\r
sleep 0.25
echo -en ' Rebooting Slave-Node-2   \' \\r
sleep 0.25
done
echo -en ' Rebooting Slave-Node-2   OK!' \\r
echo

ssh root@node3 "reboot" > /dev/null
echo
for i in {1..5}
do
echo -en " Rebooting Slave-Node-3   -" \\r
sleep 0.25
echo -en " Rebooting Slave-Node-3   /" \\r
sleep 0.25
echo -en " Rebooting Slave-Node-3   |" \\r
sleep 0.25
echo -en ' Rebooting Slave-Node-3   \' \\r
sleep 0.25
done
echo -en ' Rebooting Slave-Node-3   OK!' \\r
echo " "
echo " "
echo "                                           -------- Cluster Nodes Rebooted -------- "
echo
echo
}}}


= TITAN's High Availability =

- TITAN is a precious box for the Cluster, and with out it all the Cluster would go out of service.

- For this Reason a High Availability Solution has been applied.

- The methodology of PASSIVE-ACTIVE protocol has been used, using VRRP and heartbeats between a MASTER TITAN and a clone SLAVE-TITAN.

To do so, follow below steps:

1- Install the HA with VRRP and HBs Module, refer to:

{{{
http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/keepalive-heartbeat-vrrp
}}}

2- Configure the keepalive Daemon with the desired floating IP, which will be used '''FOR THE SERVICE'''.

'''@/etc/keepalived/keepalived.conf'''

{{{
global_defs {
             router_id haproxy1
            }
vrrp_script CHK-LB {
                      script "killall -0 haproxy"
                      interval 2
                      weight 2
                    }
vrrp_instance 50 {
                      virtual_router_id 50
                      advert_int 1
                      priority 100
                      state MASTER
                      interface eth0
                      virtual_ipaddress {
                                            192.168.0.75 dev eth0
                                        }
                      track_script {
                                    CHK-LB
                                   }
                 }


vrrp_instance 51 {
                      virtual_router_id 51
                      advert_int 1 
                      priority 100
                      state MASTER 
                      interface eth1 
                      virtual_ipaddress { 
                                            10.10.10.10 dev eth1
                                        }
                      track_script {
                                    CHK-LB
                                   } 
                 }  
}}}

- The floating IP should be different than those configured for eth0 in both master and slave.

- The idea here is to keep the eth0 IPs for managing the boxes, however the Floating IP is for the Provided service.

= Adding a Domain under protection =

- Using The system GUI, we can add new domains we want to use for this service.

- The GUI is addressed @ https://filter.noor.com/

== Adding the domain ==

'''1-''' First go to "Route" Tab.

{{attachment:domain1.jpg}}

- You will see the list of domains that are under the system protection.

'''2-''' Click on the "+" sign at the bottom of the list, to add a new domain.

{{attachment:domain2.jpg}}

'''3-''' Enter the domain name (EX: test.com) and add the IP Address of the domain MX (Mail server)

* Note : Use brackets when entering the IP Address (EX: [192.168.0.1] )

{{attachment:domain3.jpg}}

4- Finally when you are done, click on "Apply".

== Configuring Parameters ==

'''1-''' To configure the domain minimum and maximum allowed mail score, click on "quarantine" at the domain name

{{attachment:domain4.jpg}}

* Notes: 

- Above configuration means that Score below 5.0 will be passed, from 5.0 to 8.0 will be treated as spam, above 8.0 will be deleted.
    
- Mails that are considered Spam "From 5.0 to 8.0" will be quarantined to "spamgrp".

- Also you can tick to delete the virus mails and not quarantine them, or send them to spamgrp.

'''2-''' To Configure Trusted Networks, usually for trusted MX servers that the customer receives mail from (EX: Banks), simply click on "INBOUND" and enter the Truseted IPs/Networks with their CIDRs
   
 (EX: 192.168.0.8/32 ) for single IP  
    
 (EX: 192.168.0.0/24 ) for full /24 subnet


{{attachment:domain5.jpg}}

'''3-''' Click on "Apply" to save the configuration

== Removing a Domain ==

- Simpy click on the "X" beside the domain name.

{{attachment:domain6.jpg}}
