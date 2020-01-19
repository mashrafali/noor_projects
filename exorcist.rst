{{{
Author       : Mohamed Ashraf
Title        : Project Exorcist
Version      : 1.0
Creation date: 8/12/2014
Latest update: 8/12/2014
}}}

<<TableOfContents(4)>>


{{attachment:Internet-policy-word-cloud.jpg}}



= Introduction =

- In our effort to protect our ADSL users from Child abusive and/or child sexual content, we have created and test-deployed an idea (Project EXORCIST).

- The Exorcist box is mainly a Debian based Linux router, relying on the Open-source tool "Quagga".

= The Idea =

- The idea in Exorcist is to have this box directly connected with our BNG, and to initiate BGP routing between them.

'''''At which it will perform the below:'''''

1- The Exorcist Box will be fed a Black-listed URLs Database.

2- On DataBase recieval or updates, Exorcist will proceed to resolve all the URLS in the Database and convert them to IPs.

3- Next it will efficiently re-configure itself and inject all the resolved IPs into the BGP routing protocol.

4- Our BNG will receive those routes, with Exorcist marked as the next and most priority Hop.

5- Any Traffic directed to Exorcist will be simply dropped.

6- For HTTP Traffic directed to Exorcist, an Apache Web server has been installed and configured to give out only one response, A "Sorry this site is bad" message to the requesting client.

= Building Exorcist =

- The work done to create Exorcist, has been put together in the following '''AUTO-DEPLOY''' script.

- Simply copy over the following and execute it as root.


'''''EXORCIST-Deploy.sh'''''

{{{

 /bin/bash    <-- Your Parser , precede with "#!"

############## RUN AS ROOT
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo "                                               Exorcist Project Deployment   "
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
apt-get update
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get autoremove -y
apt-get autoclean
apt-get install ssh htop pydf unzip iftop snmpd snmp make nano tcpdump gcc sudo dnsutils -y
apt-get install traceroute
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
echo
echo -en " "
echo "vm.swappiness = 0" >> /etc/sysctl.conf
sysctl -w vm.swappiness=0
echo
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
echo Exorcist > /etc/hostname
sleep 1
echo "[OK]"
echo
echo -en " Configuring HOSTS File....."
echo "
127.0.0.1       localhost.localdomain localhost
$MYIP           Exorcist.noor.net Exorcist

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
" > /etc/hosts
sleep 1
echo "[OK]"
echo
echo " #################################### Installing BGP Router SYSTEM ####################################"
sleep 1
echo
apt-get install quagga quagga-doc -y
echo
echo
echo -en "Updating Daemons..."
sleep 1
echo '
zebra=yes
bgpd=yes
ospfd=no
ospf6d=no
ripd=no
ripngd=no
isisd=no
babeld=no
' > /etc/quagga/daemons
echo "[OK]"
echo
echo -en "Generating Config Files..."
sleep 1
cp /usr/share/doc/quagga/examples/zebra.conf.sample /etc/quagga/zebra.conf
cp /usr/share/doc/quagga/examples/bgpd.conf.sample /etc/quagga/bgpd.conf
cp /usr/share/doc/quagga/examples/vtysh.conf.sample /etc/quagga/vtysh.conf
echo "[OK]"
echo
echo -en "Adjusting Permissions..."
sleep 1
chown quagga.quaggavty /etc/quagga/*.conf
chmod 640 /etc/quagga/*.conf
echo "[OK]"
echo

echo -en "Configuring Initial Parameters..."
sleep 1

cp /etc/quagga/zebra.conf /etc/quagga/zebra.conf.BAK
echo '
hostname Exorcist
password n00rinfra
enable password n00rinfra
' > /etc/quagga/zebra.conf

cp /etc/quagga/bgpd.conf /etc/quagga/bgpd.conf.BAK
echo '
hostname Exorcist
password n00rinfra
enable password n00rinfra
router bgp 7675
log stdout
' > /etc/quagga/bgpd.conf

cp /etc/quagga/vtysh.conf /etc/quagga/vtysh.conf.BAK
echo '
service integrated-vtysh-config
hostname Exorcist.rams.LX1404
username root nopassword
' > /etc/quagga/vtysh.conf

echo "[OK]"
echo
echo -en "Enabling IP Forwarding..."
sleep 1
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
echo "[OK]"
echo
echo -en "Adjusting Environment Variables..."
sleep 1
echo VTYSH_PAGER=more >> /etc/environment
echo "[OK]"
echo
echo
echo " #################################### Installing Web Interfacing SYSTEM ############################"
sleep 2
apt-get install apache2 -y
echo
echo
echo -en " #### Enabling Necessary Modules..."
sleep 3
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
a2enmod mem_cache
a2enmod disk_cache
echo
echo
echo -en "Configuring web-server Directives..."
sleep 1
echo '
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:443>
        ServerAdmin webmaster@localhost
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        Redirect / http://$MYIP/
        SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
        SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
</VirtualHost>
' > /etc/apache2/sites-available/000-default.conf
echo "[OK]"
echo
echo -en "Configuring Mem-cache..."
sleep 1
echo '
<IfModule mod_mem_cache.c>
        CacheEnable mem /
        MCacheSize 134217728
        MCacheMaxObjectCount 100
        MCacheMinObjectSize 1
        MCacheMaxObjectSize 4194304 
</IfModule>
' >/etc/apache2/mods-available/mem_cache.conf
echo "[OK]"
echo
echo -en "Configuring Disk-cache..."
sleep 1
echo '
<IfModule mod_disk_cache.c>
# cache cleaning is done by htcacheclean, which can be configured in
# /etc/default/apache2
#
# For further information, see the comments in that file,
# /usr/share/doc/apache2.2-common/README.Debian, and the htcacheclean(8)
# man page.

        # This path must be the same as the one in /etc/default/apache2
        CacheRoot /var/cache/apache2/mod_disk_cache

        # This will also cache local documents. It usually makes more sense to
        # put this into the configuration for just one virtual host.

        CacheEnable disk /

        CacheDirLevels 5
        CacheDirLength 3
</IfModule>
' >> /etc/apache2/mods-available/disk_cache.conf
echo "[OK]"
echo
echo -en "Configuring Cache-Lock..."
sleep 1
echo '
#ENABLE CACHE-LOCK
<IfModule mod_cache.c>
CacheLock on
CacheLockPath /tmp/mod_cache-lock
CacheLockMaxAge 5
</IfModule>
' >> /etc/apache2/apache2.conf
echo "[OK]"
echo
echo -en " #### Revising Necessary Modules..."
sleep 3
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
a2enmod mem_cache
a2enmod disk_cache
echo
echo
echo "     ---> Modules Installation Complete! <---"
echo
echo " #################################### Post Installation Configuration ############################"
sleep 2
echo
echo -en "Configuring Login Parameters..."
sleep 2
cd /root
cat <<EOF >> /root/.bashrc
##### BANNER
TotalMem=$(free -h |grep -i mem |awk -F ' ' '{print $2}')
UsedMem=$(free -h |grep -i "+" |awk -F ' ' '{print $3}')

TotalSwap=$(free -h |grep -i swap |awk -F ' ' '{print $2}')
UsedSwap=$(free -h |grep -i swap |awk -F ' ' '{print $3}')

MYIP=$(ifconfig eth0 | grep -i "inet addr" | cut -d ":" -f2 | cut -d " " -f1)

echo "                     
                  ***
         * *      ***      * *
       ** *                 * **
      ** *                   * **
     ** **  *             *  ** **
     ** **  *             *  **  **
    **  **   *           **  **  **
    **  **    **       ***   **  ***
    **   ***   *********    ***  ***
    ***  ****             ****  ****
    ***    ****          ****   ***
    ****    ***************    ****
     *****     *********     *****
      ******               ******
       ********         ********
         *********************
           *****************
               *********  

**      *    ****        ****    ********
***     *  ***  ***    ***  ***  **     **
*  **   * **      **  **      ** **     **
*   **  * **      **  **      ** *******
*    ****  **    **    **    **  **    **
*      **   ******      ******   **     **
   ____  __  ___  __    ___  _____  __  _____   
  /__\ \/ / /___\/__\  / __\ \_   \/ _\/__   \   
 /_\  \  / //  // \// / /     / /\/\ \   / /\/  
//__  /  \/ \_// _  \/ /___/\/ /_  _\ \ / /   
\__/ /_/\_\___/\/ \_/\____/\____/  \__/ \/    
                                        MA
System @$MYIP                      "
echo
echo -en " RAM : $UsedMem/$TotalMem          "
echo "Swap: $UsedSwap/$TotalSwap"
echo
mpstat -P ALL | tail -4 | head -2
##### ROUTER PUSH IN
cd /etc/quagga/
vtysh
EOF
echo "[OK]"
echo
echo -en "Allowing Root Remote Access..."
sleep 1
sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
echo "[OK]"
echo
echo -en "Building Black-List DataBase Environment..."
sleep 3
rm /etc/update-motd.d/00-header
rm /etc/update-motd.d/10-help-text
rm /etc/update-motd.d/50-landscape-sysinfo
rm /etc/update-motd.d/90-updates-available
rm /etc/update-motd.d/91-release-upgrade
cd /etc/quagga
mkdir BLACKLIST-DB
touch BLDB-BGP
touch BLDB-IP
touch BLDB-URL
touch RESOLVE-REINJECT
chmod a+x RESOLVE-REINJECT
echo "[OK]"
echo
echo -en "Building BL-DB to BGP Injection Kit..."
sleep 5
cd /etc/quagga/BLACKLIST-DB
cat <<EOF > /etc/quagga/BLACKLIST-DB/RESOLVE-REINJECT
#! /bin/bash

clear
echo
echo
echo "                        Resolving DataBase URLs and Re-Injecting BGP"
echo "                                 Script by Mohamed Ashraf "
echo
echo

echo "################################# Defining Variables #################################"
echo
sleep 1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
echo "Environment at : $PATH"
echo
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
echo "Exorcist IP : $MYIP"
echo
echo -en "Setting Start Time..."
sleep 1
Ttime1=$(date +%r)
echo "[OK]"
echo
sleep 1
echo "################################# Cooking material for DIG to understand #################################"
sleep 1
echo

echo -en "Removing preleading (http://) if exist..."
sleep 1
sed -i 's#http://##g' /etc/quagga/BLACKLIST-DB/BLDB-URL
echo "[OK]"
echo

echo -en "Removing post leading (/) if exist..."
sleep 1
sed -i 's#/##g' /etc/quagga/BLACKLIST-DB/BLDB-URL
echo "[OK]"
echo
sleep 1
echo "################################# Starting Dig Process and Cooking results #################################"
echo
echo -en "Phase 1: Fetching From DataBase and Resolving URLs [This will take some time]..."
sleep 1
dig @8.8.8.8 +short -4 -f /etc/quagga/BLACKLIST-DB/BLDB-URL > /etc/quagga/BLACKLIST-DB/BLDB-IP.temp1
echo "[OK]"
echo

echo -en "Phase 2: Detecting unresolved urls..."
sleep 1
grep -v '[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}' /etc/quagga/BLACKLIST-DB/BLDB-IP.temp1 > /etc/quagga/BLACKLIST-DB/BLDB-IP.temp2
echo "[OK]"
echo

echo -en "Phase 3: Re-resolving missed and adding to original [This might take some time]..."
sleep 1
dig @8.8.8.8 +short -4 -f /etc/quagga/BLACKLIST-DB/BLDB-IP.temp2 >> /etc/quagga/BLACKLIST-DB/BLDB-IP.temp1
echo "[OK]"
echo

echo -en "Re-phasing just to make sure..."
sleep 1
grep -v '[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}' /etc/quagga/BLACKLIST-DB/BLDB-IP.temp1 > /etc/quagga/BLACKLIST-DB/BLDB-IP.temp2
dig @8.8.8.8 +short -4 -f /etc/quagga/BLACKLIST-DB/BLDB-IP.temp2 >> /etc/quagga/BLACKLIST-DB/BLDB-IP.temp1
echo "[OK]"
echo

echo -en "Phase 4: Extracting Pattern and Generating Resolved IPs..."
sleep 1
grep -E -o '(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)' /etc/quagga/BLACKLIST-DB/BLDB-IP.temp1 > /etc/quagga/BLACKLIST-DB/BLDB-IP.temp3
echo "[OK]"
echo

echo -en "Phase 5: Detecting entries and De-Duplicating if exists..."
sleep 1
awk '!a[$0]++' /etc/quagga/BLACKLIST-DB/BLDB-IP.temp3 > /etc/quagga/BLACKLIST-DB/BLDB-IP
echo "[OK]"
echo

echo -en "phase 6: Cleaning Temps..."
sleep 1
rm /etc/quagga/BLACKLIST-DB/BLDB-IP.temp1
rm /etc/quagga/BLACKLIST-DB/BLDB-IP.temp2
rm /etc/quagga/BLACKLIST-DB/BLDB-IP.temp3
echo "[OK]"
echo


echo "################################# BGP Injection Process #################################"
echo

echo -en "Phase 1: Cooking IP List for bgp injection - inserting pre (network)..."
sleep 1
sed -e 's/^/ network /' /etc/quagga/BLACKLIST-DB/BLDB-IP > /etc/quagga/BLACKLIST-DB/BLDB-BGP.temp1
echo "[OK]"
echo

echo -en "Phase 2: Defining /32 subnets..."
sleep 1
sed -e 's#$#/32#' /etc/quagga/BLACKLIST-DB/BLDB-BGP.temp1 > /etc/quagga/BLACKLIST-DB/BLDB-BGP.temp2
echo "[OK]"
echo

echo -en "Phase 3: Appending (route-map) Interconnection..."
sleep 1
sed -e 's/$/ route-map exorcist/' /etc/quagga/BLACKLIST-DB/BLDB-BGP.temp2 > /etc/quagga/BLACKLIST-DB/BLDB-BGP
echo "[OK]"
echo

echo -en "Phase 4: Cleaning Temps..."
sleep 1
rm /etc/quagga/BLACKLIST-DB/BLDB-BGP.temp1
rm /etc/quagga/BLACKLIST-DB/BLDB-BGP.temp2
echo "[OK]"
echo

echo -en "Phase 5: Injecting Initial Parameters..."
sleep 1
echo "!
log stdout
!
password n00rinfra
enable password n00rinfra
!
interface eth0
 description LOCAL ETH0
 ip address $MYIP/24
 ipv6 nd suppress-ra
!
interface lo
 ip address 1.1.1.1/24
!
ip forwarding
!
line vty
 anonymous restricted
 exec-timeout 5 0
!
route-map exorcist permit 10
 set aggregator as 65002 1.2.3.4
 set as-path prepend 1 2 3 4
 set atomic-aggregate
 set community 65000:1 additive
 set extcommunity rt 65000:1
 set metric 20
 set originator-id 1.2.3.4
!
router bgp 65000
 bgp router-id 192.168.0.136
 neighbor 192.168.0.138 remote-as 65001" > /etc/quagga/Quagga.conf
echo "[OK]"
echo

echo -en "Phase 6: Injecting BlackListed IPS..."
sleep 1
cat /etc/quagga/BLACKLIST-DB/BLDB-BGP >> /etc/quagga/Quagga.conf
echo "!" >> /etc/quagga/Quagga.conf
echo "!" >> /etc/quagga/Quagga.conf
echo "[OK]"
echo
echo "################################# restarting BGP Session #################################"
echo
/etc/init.d/quagga restart
echo
echo
echo -en "Setting End Time..."
sleep 1
Ttime2=$(date +%r)
echo "[OK]"
echo
echo "                ------------------> ROUTES INJECTION SUCCESS <------------------"
echo
echo "- Start Time : $Ttime1"
echo "- End Time   : $Ttime2"
EOF
echo "[OK]"
echo
echo "     ---> Post Installation Procedures Completed! <---"
echo
echo " #################################### Rebooting System ############################"
sleep 2

reboot
echo
}}}
