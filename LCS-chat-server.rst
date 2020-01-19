{{{
Author       : Mohamed Ashraf
Title        : LCS: Agent-Client Live Support Chat Server
Version      : 1.0
Creation date: 12/01/2015
Latest update: 12/01/2015
}}}

<<TableOfContents(4)>>


{{attachment:logo.jpg}}


= Features =

'''''The System is capable of the following:'''''

1- The solution as a whole is built for Agent-Client Purpose.

2- Clients Access anonymously.

3- Agents Segregation by Departments.

4- Chat History for every user and/or Department, also Live view of any
undergoing chat by the admin user.

5- Is not based on Queue, but "Pendings" where any member of the
targeted department can pick up whatever customer in the pending list.

6- Automatic Response for Clients on first open chat, also Delayed 2nd
response with customizable messages & delay period.

7- Customer can choose which department he wants support from.

8- Pre-Saved Editable Messages for Agents, where they can simply click
and send them on certain occasions.

9- Embed the chat Widget in any up and running Website.

10 - Embed Questionnaire, FAQ, Chatbox, Offers, Forms and documents to
any live website.

- We have mentioned the most important features, however it appears to
have more, that we can utilize.


= System Built =

- An '''Auto-Deploy Script''' has been created which aggregates all necessary steps for system installation.

- To save any future Deployments time, copy over the below script and run as ''root''.

'''''LCS-Auto-Deploy.sh'''''
{{{

 /bin/bash   <-- Your Parser, precede with "!#"

############## RUN AS ROOT
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo "                                             LCS Chat Server Auto Deployment  "
echo "                                                Script by Mohamed Ashraf "
echo
echo
echo " -> Please run this script as root! "
echo
echo
echo " >>>>> BEFORE YOU BEGIN: Please attach the Vm-waretools CD to the machine & hit enter when you are done <<<<<"
read
sleep 2
echo " #################################### PREPARING SYSTEM ############################"
sleep 1
echo
echo -en "Configuring Apt-Cache Server Link..."
sleep 2
echo '
Acquire::http { Proxy "http://192.168.0.114:3142"; };
' > /etc/apt/apt.conf.d/02apt-cacher
echo "[OK]"
echo
sleep 1
apt-get update
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get autoremove -y
apt-get autoclean
apt-get install ssh htop pydf unzip iftop snmpd snmp make nano tcpdump gcc sudo dnsutils -y
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get autoremove -y
apt-get autoclean
echo
echo
echo " #################################### INSTALLING VM-WARE TOOLS ############################"
sleep 2
mkdir /mnt/vm-tools
mount /dev/cdrom /mnt/vm-tools
mkdir /root/vm-tools
cp /mnt/vm-tools/VMwareTools-* /root/vm-tools
cd /root/vm-tools/
tar zxvf VMwareTools-*
echo
echo
echo " #################################### CONFIGURE VM-WARE TOOLS ############################"
echo
echo
/root/vm-tools/vmware-tools-distrib/vmware-install.pl
echo
echo
echo " #################################### Optimizing Swap ####################################"
sleep 1
echo
echo " Configuring System Not to Swap unless to avoid System out of memory....."
sleep 2
echo
echo -en " "
echo "vm.swappiness = 0" >> /etc/sysctl.conf
sysctl -w vm.swappiness=0
echo
sleep 2
echo " Configuration OK!"
echo
echo
echo " #################################### Finalizing Environment  ############################"
sleep 2
echo
echo -en " Configuring network management services....."
cd /etc/snmp/
cp snmpd.conf snmpd.conf.BACKUP

echo "
#  Listen for connections from the local system only
agentAddress  udp:127.0.0.1:161
#  Listen for connections on eth0 only
agentAddress  udp:$MYIP:161

## System location and contact information
syslocation NOOR
syscontact NOOR-SYSTEMS

rocommunity N0oRiNfR@
" > snmpd.conf
sleep 1
echo "[OK]"
echo
echo -en " "
echo -en $(/etc/init.d/snmpd restart)
sleep 1
echo "....[OK]"
echo
echo -en " Applying new HOSTNAME....."
echo LCS > /etc/hostname
sleep 1
echo "[OK]"
echo
echo -en " Configuring HOSTS File....."
echo "
127.0.0.1       localhost.localdomain localhost
$MYIP           LCS.noor.net LCS

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
" > /etc/hosts
sleep 1
echo "[OK]"
echo
echo
echo " #################################### Building LCS SYSTEM ############################"
sleep 2
echo
echo " Installing Dependencies....."
sleep 2
echo
apt-get install php5 mysql-server -y
echo "[OK]"
echo
sleep 1
echo " Building Environment....."
sleep 1
echo
cd /root
mkdir LCS
cd LCS
wget https://codeload.github.com/LiveHelperChat/livehelperchat/zip/master
mv master LCS.zip
unzip LCS.zip 
sleep 2
echo
echo "[OK]"
echo
sleep 1
for i in {1..5}
do
echo -en " Installing LCS System.....  -" \\r
sleep 0.25
echo -en " Installing LCS System.....  /" \\r
sleep 0.25
echo -en " Installing LCS System.....  |" \\r
sleep 0.25
echo -en ' Installing LCS System.....  \' \\r
sleep 0.25
done
echo -en " Installing LCS System..... [OK]" \\r
rm /var/www/html/index.html
cd /root/LCS/livehelperchat-master/lhc_web
cp -r * /var/www/html/
echo
echo -en "Adjusting Permissions..."
cd /var/www/html/
chmod -R 777 cache/
chmod -R 777 settings/
chmod -R 777 var/storage/
chmod -R 777 var/userphoto/
chmod -R 777 var/storageform/
chmod -R 777 var/storagedocshare/
chmod -R 777 var/storagetheme/
chmod -R 777 var/tmpfiles/
sleep 1
echo "[OK]"
echo
echo "Installing Extensions..."
echo
apt-get install php5-curl -y
apt-get install php5-gd php5-mysql -y
service apache2 restart 
sleep 2
echo "[OK]"
echo
echo -en "Installing NOOR Brand..."
cd /var/www/html/design/defaulttheme/tpl
sed -i 's/Powered by Live Helper Chat/Powered by NOOR INFRASTRUCTURE SYSTEMS/g' lhabstract/custom/widget_theme.tpl.php
sed -i 's/Powered by Live Helper Chat/Powered by NOOR INFRASTRUCTURE SYSTEMS/g' lhchat/widget_brand/chatbox.tpl.php
sed -i 's/Powered by Live Helper Chat/Powered by NOOR INFRASTRUCTURE SYSTEMS/g' lhchat/widget_brand/live_help.tpl.php
sed -i 's/Powered by Live Helper Chat/Powered by NOOR INFRASTRUCTURE SYSTEMS/g' lhchat/widget_brand/faq.tpl.php
sed -i 's/Powered by Live Helper Chat/Powered by NOOR INFRASTRUCTURE SYSTEMS/g' lhchat/widget_brand/questionary.tpl.php
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' lhabstract/custom/widget_theme.tpl.php
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' lhchat/widget_brand/chatbox.tpl.php
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' lhchat/widget_brand/live_help.tpl.php
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' lhchat/widget_brand/faq.tpl.php
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' lhchat/widget_brand/questionary.tpl.php
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' pagelayouts/parts/page_footer.tpl.php
sed -i 's#Live Helper Chat#NOOR NSP#g' pagelayouts/parts/page_footer.tpl.php
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' pagelayouts/parts/page_footer_user.tpl.php
sed -i 's#Live Helper Chat#NOOR NSP#g' pagelayouts/parts/page_footer_user.tpl.php
cd /var/www/html/cache/compiledtemplates
sed -i 's#http://livehelperchat.com#http://www.noor.net/en/#g' *
sed -i 's#Powered by Live Helper Chat#Powered by NOOR INFRASTRUCTURE SYSTEMS#g' *
sed -i 's#'Live Helper Chat'#'NOOR DATA NETWORKS'#g' *
sed -i 's#NOOR DATA NETWORKS &#NOOR NSP &#g' *
sed -i 's#NOOR NSP NOOR DATA NETWORKS#NOOR NSP#g' *
sed -i 's#Live Support#NOOR Support#g' *
cd /var/www/html/modules/lhchat/
sed -i 's#Live Support#NOOR Support#g' * 
sed -i 's#translation>Live Support<#translation>NOOR Support<#g' /var/www/html/translations/de_DE/translation.ts
sed -i 's#translation>Live Support<#translation>NOOR Support<#g' /var/www/html/translations/da_DA/translation.ts
sed -i 's#translation>Live Support<#translation>NOOR Support<#g' /var/www/html/translations/de_DE/translation.ts
sed -i 's#Live Support#NOOR Support#g' /var/www/html/lib/core/lhchat/lhchatworkflow.php
sleep 2
echo "[OK]"
echo
echo
echo " #################################### Rebooting System ############################"
echo
echo "                              LCS SYSTEM BOOTSTRAP IS COMPLETE"
echo
echo "                                 Access settings via http"
sleep 2
echo
echo "                             !! The system will now reboot !! "
echo "                                  Hit Enter to Continue        "
read
echo
echo
reboot
echo
}}}

-----------------------------------------------------------------------------------------------------

{{attachment:openfire.png}}

= OpenFire Chat Server =

- An '''Auto-Deploy Script''' has been created which aggregates all necessary steps for system installation.

- To save any future Deployments time, copy over the below script and run as ''root''.

'''''OPENFIRE-Auto-Deploy.sh'''''
{{{

 /bin/bash   <-- Your Parser, precede with "!#"

############## RUN AS ROOT
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo "                                             OpenFire Chat Server Deployment  "
echo "                                                Script by Mohamed Ashraf "
echo
echo
echo " -> Please run this script as root! "
echo
echo
echo " >>>>> BEFORE YOU BEGIN: Please attach the Vm-waretools CD to the machine & hit enter when you are done <<<<<"
read
sleep 2
echo " #################################### PREPARING SYSTEM ############################"
sleep 1
echo
echo -en "Configuring Apt-Cache Server Link..."
sleep 2
echo '
Acquire::http { Proxy "http://192.168.0.114:3142"; };
' > /etc/apt/apt.conf.d/02apt-cacher
echo "[OK]"
echo
sleep 1
apt-get update
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get autoremove -y
apt-get autoclean
apt-get install ssh htop pydf unzip iftop snmpd snmp make nano tcpdump gcc sudo dnsutils -y
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get autoremove -y
apt-get autoclean
echo
echo
echo " #################################### INSTALLING VM-WARE TOOLS ############################"
sleep 2
mkdir /mnt/vm-tools
mount /dev/cdrom /mnt/vm-tools
mkdir /root/vm-tools
cp /mnt/vm-tools/VMwareTools-* /root/vm-tools
cd /root/vm-tools/
tar zxvf VMwareTools-*
echo
echo
echo " #################################### CONFIGURE VM-WARE TOOLS ############################"
echo
echo
/root/vm-tools/vmware-tools-distrib/vmware-install.pl
echo
echo
echo " #################################### Optimizing Swap ####################################"
sleep 1
echo
echo " Configuring System Not to Swap unless to avoid System out of memory....."
sleep 2
echo
echo -en " "
echo "vm.swappiness = 0" >> /etc/sysctl.conf
sysctl -w vm.swappiness=0
echo
sleep 2
echo " Configuration OK!"
echo
echo
echo " #################################### Finalizing Environment  ############################"
sleep 2
echo
echo -en " Configuring network management services....."
cd /etc/snmp/
cp snmpd.conf snmpd.conf.BACKUP

echo "
#  Listen for connections from the local system only
agentAddress  udp:127.0.0.1:161
#  Listen for connections on eth0 only
agentAddress  udp:$MYIP:161

## System location and contact information
syslocation NOOR
syscontact NOOR-SYSTEMS

rocommunity N0oRiNfR@
" > snmpd.conf
sleep 1
echo "[OK]"
echo
echo -en " "
echo -en $(/etc/init.d/snmpd restart)
sleep 1
echo "....[OK]"
echo
echo -en " Applying new HOSTNAME....."
echo Infra-Server > /etc/hostname
sleep 1
echo "[OK]"
echo
echo -en " Configuring HOSTS File....."
echo "
127.0.0.1       localhost.localdomain localhost
$MYIP           Infra-Server.noor.net Infra-Server

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
" > /etc/hosts
sleep 1
echo "[OK]"
echo
echo
echo " #################################### Building OpenFire System ############################"
sleep 2
echo

echo " Installing Dependencies....."
sleep 2
echo
rm /etc/apt/apt.conf.d/02apt-cacher
add-apt-repository ppa:webupd8team/java -y
apt-get update
apt-get install oracle-java8-installer -y
echo "[OK]"
echo
echo " Building Environment....."
cd ~
mkdir openfire
cd openfire
wget http://download.igniterealtime.org/openfire/openfire_3.9.3_all.deb
sleep 1
echo "[OK]"

echo " Installing OpenFire Chat System....."
cd ~
cd openfire
dpkg -i openfire_3.9.3_all.deb
dpkg -i openfire_3.9.3_all.deb
sleep 1
echo "[OK]"

echo
echo
echo " #################################### Rebooting System ############################"
sleep 2
echo
echo "                             !! The system will now reboot !! "
echo "                                  Hit Enter to Continue        "
read
echo
echo
reboot
echo

}}}
