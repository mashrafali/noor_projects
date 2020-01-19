{{{
Author       : Mohamed Ashraf
Title        : Linux Repository Cache Server
Version      : 1.0
Creation date: 9/12/2014
Latest update: 9/12/2014
}}}

<<TableOfContents(4)>>


{{attachment:logo1.jpg}}


= Introduction =

- With the increasing number of Linux based boxes in our network, we needed to create an apt-cache service

'''To apply the following:'''

1- Increase Download speeds for Linux packages, thus saving time.

2- Decrease consumed bandwidth used for Linux packages.

3- Retain the ability to install Packages for Backend non-internet connecting servers.


= System Building =

- The first step is to make sure that you have configured a ''Static IP'' for the server.

- Next, Starts the cacher system building.

- All Work done to build and configure the server has been put together in the below '''''AUTO-DEPLOY''''' Script.


'''''Apt-Cacher-AUTO-DEPLOY.sh'''''
{{{

 /bin/bash   <--- Parser, precede with "#!"

############## RUN AS ROOT
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo "                                              Apt-Cacher Server Deployment  "
echo "                                                Script by Mohamed Ashraf "
echo
echo
echo " -> Please run this script as root! "
echo
echo
echo " >>>>> BEFORE YOU BEGIN: Please attach the Vm-waretools CD to the machine & hit enter when you are done <<<<<"
read
sleep 2
echo " #################################### Building Cacher System ############################"
sleep 1
echo
apt-get update
apt-get install apt-cacher-ng -y
echo
echo
echo -en "Backing up Configuration..."
sleep 1
cp /etc/apt-cacher-ng/acng.conf /etc/apt-cacher-ng/acng.conf.BACKUP
echo "[OK]"
echo
echo -en "Building new Configuration..."
sleep 3
echo '
CacheDir: /APT-CACHED
LogDir: /var/log/apt-cacher-ng
Port:3142
BindAddress: 0.0.0.0
Remap-debrep: file:deb_mirror*.gz /debian ; file:backends_debian # Debian Archives
Remap-uburep: file:ubuntu_mirrors /ubuntu ; file:backends_ubuntu # Ubuntu Archives
Remap-debvol: file:debvol_mirror*.gz /debian-volatile ; file:backends_debvol # Debian Volatile Archives
Remap-cygwin: file:cygwin_mirrors /cygwin # ; file:backends_cygwin # incomplete, please create this file or specify preferred mirrors here
Remap-sfnet:  file:sfnet_mirrors # ; file:backends_sfnet # incomplete, please create this file or specify preferred mirrors here
Remap-alxrep: file:archlx_mirrors /archlinux # ; file:backend_archlx # Arch Linux
Remap-fedora: file:fedora_mirrors # Fedora Linux
Remap-epel:   file:epel_mirrors # Fedora EPEL
Remap-slrep:  file:sl_mirrors # Scientific Linux
ReportPage: acng-report.html
VerboseLog: 1
PidFile: /var/run/apt-cacher-ng/pid
ExTreshold: 4
' > /etc/apt-cacher-ng/acng.conf
echo "[OK]"
echo
echo -en "Preparing Cache Directory..."
sleep 1
mkdir /APT-CACHED
chown apt-cacher-ng:apt-cacher-ng /APT-CACHED
chmod 2755 /APT-CACHED/
echo "[OK]"
echo
echo "Rebuilding System with New Configuration..."
echo "-------------------------------------------"
sleep 1
echo
apt-get remove apt-cacher-ng -y
apt-get install apt-cacher-ng -y
echo
echo "[OK]"
echo
echo " #################################### PREPARING SYSTEM ############################"
sleep 1
echo
echo -en "Configuring Apt-Cache Server Link..."
sleep 2
cat <<EOF > /etc/apt/apt.conf.d/02apt-cacher
Acquire::http { Proxy "http://$MYIP:3142"; };
EOF
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
echo " #################################### Rebooting System  ############################"
sleep 2

reboot
echo
}}}

= Client to server Link =

- In order for the client to pass through the Cacher when fetching packages, we can apply the following

== For Newly Deployed Linux Boxes ==

- For newly Deployed Boxes, use the initialization script found below

http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/ubuntu-Initialization

- The Script will automatically link the linux server to its Cacher Service.

== For Production Linux Boxes ==

- Simply copy over below tiny script and execute it as '''ROOT'''

{{{
echo
echo -en "Configuring Apt-Cache Server Link..."
sleep 2
echo '
Acquire::http { Proxy "http://192.168.0.114:3142"; };           # EDIT THIS LINE TO MATCH THE SERVER IP NATTING IF YOU ARE CONFIGURING FROM RAMSIS
' > /etc/apt/apt.conf.d/02apt-cacher
echo "[OK]"
echo
}}}

= Deployed NOOR Cacher =

- An Apt-Cache service has been built and deployed @192.168.0.114 Static Natted to 217.139.224.233

- Ramsis Speed Test Server also acts as an Apt-cacher @217.139.1.18
