Author       : Mohamed Ashraf
Title        : Active Anti-Virus module
Version      : 1.0
Creation date: 26/1/2015
Latest update: 26/1/2015

= Introduction

- The open Source Tool that is going to be used for this module is "ClamAV".

- ClamAV is an open source (GPL) anti-virus engine used in a variety of situations including email scanning, web scanning, and end point security. 

- It provides a number of utilities including a flexible and scalable multi-threaded daemon, a command line scanner and an advanced tool for automatic database updates.


= Mission: Auto Search & Destroy

- For most cases, AV Scan requests are done manually or on demand, however in our system we need this task to be active and automatically.

- The purpose of this module is to Automatically scan certain directories and Purges Infections, without waiting on any user input.


= Installation 

- First install the ClamAV package and update it using freshclam, Or you can use the pre-written script for this task:

{{{
 /bin/bash    <-- Your Parser

echo
echo " #################################### INSTALLING ACTIVE ANTI-VIRUS ############################"
sleep 2
echo
echo "Installing ClamAV Software..."
echo
sleep 1
apt-get install clamav-daemon -y
echo
echo "[OK]"
echo
echo "Updating Definitions:"
sleep 1
freshclam
echo "[OK]"
echo
echo "Registering services..."
/etc/init.d/clamav-daemon start
echo "[OK]"
echo
echo
}}}

- you can use the service by entering the following command:

{{{
clamdscan /DIR-TO-SCAN/ --fdpass --log=/DIR-TO-LOG/clamav-scan.log --infected --multiscan
}}}


= Automation 

- To activate the automation feature, we have create below script to be used in the system

SEARCH-AND-DESTORY.SH
{{{
 /bin/bash <--Parser


cd /root/Anti-Virus/

echo
#Emtpy the old scanlog and do a virus scan
rm -R /root/Anti-Virus/clamav-scan.log
touch /root/Anti-Virus/clamav-scan.log
clamdscan /Media-Content/ --fdpass --log=/root/Anti-Virus/clamav-scan.log --infected --multiscan

#Delete Infected
echo
echo -en " Purging Infections if any found..." \\r

cat clamav-scan.log | grep /Media-Content/ | cut -d ":" -f1 > clamav-scan.purge

while read virus; do
rm "$virus"
done < clamav-scan.purge
sleep 1
echo -en ' Purging Infections if any found...[OK]' \\r
echo
echo "     >> ALL CLEAN ONCE AGAIN << "
echo
}}}

- And then this script can be added to crontab for a minute by minute scan :

# crontab -e

{{{
* * * * * /root/Anti-Virus/SEARCH-AND-DESTORY.SH
}}}
