{{{
Author       : Mohamed Ashraf
Title        : Debian/Ubuntu Environment Initialization
Version      : 4
Creation date: 30/10/2014
Latest update: 28/1/2015
}}}

<<TableOfContents(4)>>


{{attachment:logo2.jpg}}




= Script Function =

'''The script has been created to perform the below actions:'''

1 - Fully Update the Linux kernel and system packages.

2 - Install basic system tool needed or used by Admins [HTOP, PYDF, SNMPD, ....].

3 - Install and Configure VM-Ware tools.

4 - Optimize system SWAP.

5 - Configuring SNMP Parameters for monitoring intention.

6 - Auto Configure Host-Name and hosts file accordingly.

7 - Finally it reboots the server after finishing Environment Initialization.


= The Script Usage =

'''In order to run the script correctly, perform the following points:'''

1 - Copy over the script content to a file on a server [Ex: init.sh]

2- turn the file to executable
{{{
chmod a+x init.sh
}}}

3- Attach the VM-ware tools CD to the server from V-Sphere client
{{{
right click on the VM >> Guest >> Install/upgrade vmware tools
}}}

4- Run the script as ''ROOT''.

= The Script Content =

The Script content is as below

{{{

/bin/bash   <-- Parser, Precede with "#!"


############## RUN AS ROOT
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo "                                         Debian/Ubuntu Environment Initialization  "
echo "                                                Script by Mohamed Ashraf "
echo
echo
echo " -> Please run this script as root! "
echo
echo
#echo " >>>>> BEFORE YOU BEGIN: Please attach the Vm-waretools CD to the machine & hit enter when you are done <<<<<"
echo " -> Hit Enter to Begin..."
read
sleep 2
echo " #################################### PREPARING SYSTEM ############################"
sleep 1
echo
echo -en "Configuring Apt-Cache Server Link..."
sleep 2
echo 'Acquire::http { Proxy "http://192.168.0.114:3142"; };' > /etc/apt/apt.conf.d/02apt-cacher
echo "[OK]"
echo
sleep 1
apt-get update
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get --purge autoremove -y
apt-get autoclean
apt-get install ssh htop pydf unzip iftop snmpd snmp make nano tcpdump gcc sudo dnsutils ntp ethtool nload fping nmap -y
apt-get install plymouth-themes plymouth-label -y
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get --purge autoremove -y
apt-get autoclean
echo
echo
#echo " #################################### INSTALLING ACTIVE ANTI-VIRUS ############################"
#(OPTIONAL)
#sleep 2
#echo
#echo "Installing ClamAV Software..."
#echo
#sleep 1
#apt-get install clamav-daemon -y
#echo
#echo "Installing ClamAV Software...[OK]"
#echo
#echo "Updating Definitions..."
#echo
#sleep 1
#freshclam
#echo
#echo "Updating Definitions...[OK]"
#echo
#echo "Registering services..."
#echo
#/etc/init.d/clamav-daemon start
#echo
#sleep 1
#echo "Registering services...[OK]"
#echo
#echo
echo " #################################### INSTALLING VM-WARE TOOLS ############################"
apt-get install open-vm-tools -y
#sleep 2
#mkdir /mnt/vm-tools
#echo
#mount /dev/sr0 /mnt/vm-tools
#mkdir /root/vm-tools
#echo
#echo -en "Copying Package....."
#cp /mnt/vm-tools/VMwareTools-* /root/vm-tools
#echo "[OK]"
#echo
#sleep 1
#cd /root/vm-tools/
#tar zxvf VMwareTools-*
#echo
#echo
#echo " #################################### CONFIGURE VM-WARE TOOLS ############################"
#echo
#echo
#/root/vm-tools/vmware-tools-distrib/vmware-install.pl --default
#echo
##apt-get install open-vm-tools -y
#echo
echo " #################################### Optimizing Swap ####################################"
sleep 1
echo
echo " Configuring System Not to Swap unless to avoid System out of memory....."
sleep 2
echo
echo -en " "
echo "vm.swappiness = 0
vm.vfs_cache_pressure = 50" >> /etc/sysctl.conf
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

echo "#  Listen for connections from the local system only
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
echo "127.0.0.1       localhost.localdomain localhost
$MYIP           Infra-Server.noor.net Infra-Server

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
" > /etc/hosts
sleep 1
echo "[OK]"
echo
echo -en " Adjusting SSH Parameters....."
sed -i 's#PermitRootLogin without-password#PermitRootLogin yes#g' /etc/ssh/sshd_config
sed -i 's/#GSSAPIAuthentication no/GSSAPIAuthentication no/g' /etc/ssh/sshd_config 
sed -i 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
echo "
#Special Config Parameters For Fast SSH
UseDNS no
" >> /etc/ssh/sshd_config
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


{{attachment:logo1.png}}
