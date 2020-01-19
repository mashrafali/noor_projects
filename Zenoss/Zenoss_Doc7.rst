{{{
Author       : Mohamed Ashraf
Title        : Sickness Discovery and Recovery
Version      : 1.0
Creation date: 6/11/2013
Latest update: 6/11/2013
}}}



{{attachment:logo1.jpg}}

<<TableOfContents(4)>>




= Symptoms =

'''- If the system is suffering from the following symptoms:'''

1- One or All of some Devices, Components, Events and reports cannot be edited/deleted or has no data.

2- Slow or Full Queuing in rabbit-server.

3- Slow Database Transactions.


= Rabbit-server queue check =

'''- Apply the following Loop Script to follow up Rabbit server Queuing:'''

{{{

#! /bin/bash

## RUN AS ROOT
while true
do

clear
rabbitmqctl list_queues -p /zenoss
sleep 2

done
}}} 

'''Result Example:'''

{{{
Listing queues ...
Zenoss.celeryd.pidbox   0
celery  0
zenoss.queues.zep.heartbeats    0
zenoss.queues.zep.migrated.archive      0
zenoss.queues.zep.migrated.summary      0
zenoss.queues.zep.modelchange   0
zenoss.queues.zep.rawevents     0
zenoss.queues.zep.signal        0
zenoss.queues.zep.zenevents     0
...done.
}}}

= Repair Script =

'''This Script contains a combined check and repair that addresses above symptoms:'''


'''''RUN AS ZENOSS USER'''''


{{{

#! /bin/bash

echo
echo "####################### ZENOSS ZODB, RELATIONS & POSKEY ERRORS FIND AND FIX ###############################"
echo "                                                                                   Script by Mohamed Ashraf"
echo
echo
echo "#### PLEASE RUN AS ZENOSS USER ####"
echo
echo " Please Read:"
echo "-------------"
echo "1- Zenoss services will be stopped during this script"
echo "2- All Events in main console and Archive will be purged"
echo "3- This will take a Very L0o0o0o0o0o0ong Time!"
echo
echo "** HIT ENTER TO CONFIRM "
read
echo
echo
echo "Here we go:"
echo
echo "############## Stopping Zenoss:"
zenoss stop
echo
echo
echo "################################################ PHASE ONE ################################################"
echo
echo

echo "############## Packing ZODB & Related Transactions:"
zenossdbpack
echo
echo

echo "############## Checking ZODB:"
zodbscan -v10
echo
echo

echo "############## Checking & attempting to fix POSKEYERRORS:"
findposkeyerror -v10 -f
echo
echo

echo "############## Checking & attempting to fix Zen-relations:"
zenrelationscan -v10 -f
echo
echo

echo "############## Checking & attempting to fix Catalog Components:"
zencatalogscan -v10 -f
echo
echo

echo "############## Checking & attempting to detect and fix broken ZODB relationships:"
zenchkrels
echo
echo

echo "############## Running Events PURGE Native Backup and restore:"
mkdir /usr/local/zenoss/backups
cd /usr/local/zenoss/backups
zenbackup --no-eventsdb --file=NATIVE-APP-BACKUP
zenrestore -v --file=NATIVE-APP-BACKUP
echo
echo

echo "############## Purging Rabbit-Server Queues and Reconfiguring Agent:"
echo anyonecan | sudo -S rabbitmqctl stop_app && echo
echo anyonecan | sudo -S rabbitmqctl reset && echo
echo anyonecan | sudo -S rabbitmqctl start_app
echo anyonecan | sudo -S rabbitmqctl add_user zenoss zenoss && echo
echo anyonecan | sudo -S rabbitmqctl add_vhost /zenoss && echo
echo anyonecan | sudo -S rabbitmqctl set_permissions -p /zenoss zenoss '.*' '.*' '.*' && echo
echo
echo

echo "################################################ PHASE TWO ################################################"
echo
echo

echo "############## Packing ZODB & Related Transactions:"
zenossdbpack
echo
echo

echo "############## Checking ZODB:"
zodbscan -v10
echo
echo

echo "############## Checking & attempting to fix POSKEYERRORS:"
findposkeyerror -v10 -f
echo
echo

echo "############## Checking & attempting to fix Zen-relations:"
zenrelationscan -v10 -f
echo
echo

echo "############## Checking & attempting to fix Cataloge Components:"
zencatalogscan -v10 -f
echo
echo

echo "############## Checking & attempting to detect and fix broken ZODB relationships:"
zenchkrels
echo
echo

echo "############## Starting Zenoss:"
zenoss start
echo
echo

}}}

= Check Monitoring Reachability to a certain device =

- From Zenoss Server apply the following command:

{{{
snmpwalk -cCOMMUNITY-NAME DEVICE-IP
}}}

- For example if community name is "N0or@NmS" and device ip is @192.168.0.145
then:
{{{
snmpwalk -cN0or@NmS 192.168.0.145
}}}

- By default this queries the device using snmp version2 (v2c), but if u need to specifiy version 1 only add the below flag:
{{{
snmpwalk -cN0or@NmS -v1 192.168.0.145
}}}

- To Check Community Name at linux Servers, apply the below command:
{{{
#sudo cat /etc/snmp/snmpd.conf | grep -i rocommunity
}}}

- To check & attempt repair of mysql database tables:
{{{
mysql_upgrade -uroot -p --force && mysqlcheck -u root -p --auto-repair --check --all-databases
}}}
