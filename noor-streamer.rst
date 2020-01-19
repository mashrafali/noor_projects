{{{
Author       : Mohamed Ashraf
Title        : NOOR Streamer Server
Version      : 1.0
Creation date: 23/12/2014
Latest update: 23/12/2014
}}}

<<TableOfContents(4)>>


{{attachment:logo1.jpg}}




= Introduction =

- Since our need for a streaming server, in order to store and instantly stream on demand whatever media content we have in NOOR (Ex: Chalk Talks)

- A Media Streaming Server - based on Plex media - has been built and configured for this purpose.

- A FFMPEG converter has been also built for any necessary uses.

= Features =

'''''- The system is capable of the following: '''''

1- Storing and organizing our Media Files.

2- Streaming almost all widely used video/audio formats.

3- Automatic updates upon file changes.

4- Local converter tool for almost any to any format conversion.

..and alot more

= Streaming Capabilities =

'''''- The User can stream videos using any of the following methods: '''''

1- Stream directly via WEB-UI.

2- Stream to our Smart TVs using the DLNA Server which the streamer server has.

3- Stream on Android/Apple Smart Phones. 

= System Built =

- An '''Auto-Deploy Script''' has been created which aggregates all necessary steps for system installation.

- To save any future Deployments time, copy over the below script and run as ''root''.

'''''Streamer-Auto-Deploy.sh'''''
{{{

 /bin/bash   <-- Your Parser, precede with "!#"

############## RUN AS ROOT
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo "                                           NOOR Streamer System Auto Deployment  "
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
echo streamer > /etc/hostname
sleep 1
echo "[OK]"
echo
echo -en " Configuring HOSTS File....."
echo "
127.0.0.1       localhost.localdomain localhost
$MYIP           streamer.noor.net streamer

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
" > /etc/hosts
sleep 1
echo "[OK]"
echo
echo
echo " #################################### Building Streamer System ############################"
echo
echo "Installing Dependencies..."
echo
sleep 2
apt-get install avahi-daemon avahi-utils -y
apt-get -f install -y
apt-get install avahi-daemon avahi-utils -y
apt-get install apache2 -y
echo
echo "Enabling Modules..."
echo
sleep 2
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
echo
sleep 2
echo -en "Creating Directives..."
sleep 1
echo '
<VirtualHost *:80>
        RewriteEngine on
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        RewriteOptions Inherit
        RewriteRule ^/(.*) http://streamer.noor.net:32400/web/$1 [P]
</VirtualHost> ' > /etc/apache2/sites-available/000-default.conf
echo "[OK]"
echo
echo -en "Building Environment..."
cd ~
mkdir Streamer-media-server
cd Streamer-media-server/
sleep 1
echo "[OK]"
echo
echo "Building Streamer System..."
echo
sleep 2
add-apt-repository ppa:jon-severinsson/ffmpeg
apt-get update
apt-get install ffmpeg -y
wget https://downloads.plex.tv/plex-media-server/0.9.11.4.739-a4e710f/plexmediaserver_0.9.11.4.739-a4e710f_amd64.deb
dpkg -i plexmediaserver_0.9.11.4.739-a4e710f_amd64.deb
echo
sleep 1
echo "[OK]"
echo
echo -en "Creating Media Directoy..."
echo
mkdir /Media-Content
chown plex:plex /Media-Content/
echo "[OK]"
echo
echo "Building Anti-Virus Features..."
echo
sleep 1
apt-get install clamav clamav-daemon -y
freshclam
/etc/init.d/clamav-daemon start
echo
sleep 1
echo "[OK]"
echo
echo
echo " #################################### Rebooting System  ############################"
sleep 2

reboot
echo
}}}

= Conversion Tool =

- First create the working directory.

- Use same directory name as it is coded in the script, or change DIRs in the Script.

{{{
mkdir /root/Convertion-Tool/
cd /root/Convertion-Tool/
}}}

- Use Below created tool for automatic video conversion

== The Script ==

- Create the following Script and add to Crontab.

{{{

 /bin/bash   <-- Your Parser, precede with "!#"

## OLD STUFF
#cd /Media-Content/Chalk-Talks/
#cd /root/Convertion-Tool
#find /Media-Content -name "*.MOV" > directories
#echo /dev/null > to-convert
#while read dir; do
#  echo '"'$dir'"' >> to-convert
#done < directories

#while read f; do
#  ffmpeg -i "$f" -s 960x540 -vcodec h264 -crf 25 -acodec aac -strict -2 -vf "movie=/root/Convertion-Tool/watermark.png [watermark]; [in][watermark] overlay=main_w-overlay_w-10:10 [out]" "${f%.MOV}.mp4"
#done < to-convert





#INIT
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
cd /Media-Content/

# Check Spaces For Convertion Feed & turn to "-"
find -name "* *" -type d | rename 's/ /-/g'    #Directories
find -name "* *" -type f | rename 's/ /-/g'    #Files

# Convert & WaterMark
for f in $(find /Media-Content -name "*.MOV"); do
   ffmpeg -i "$f" -s 960x540 -vcodec h264 -crf 25 -acodec aac -strict -2 -vf "movie=/root/Convertion-Tool/watermark.png [watermark]; [in][watermark] overlay=main_w-overlay_w-10:10 [out]" "${f%.MOV}.mp4"
   rm "$f"
done

for f in $(find /Media-Content -name "*.mov"); do
   ffmpeg -i "$f" -s 960x540 -vcodec h264 -crf 25 -acodec aac -strict -2 -vf "movie=/root/Convertion-Tool/watermark.png [watermark]; [in][watermark] overlay=main_w-overlay_w-10:10 [out]" "${f%.mov}.mp4"
   rm "$f"
done







# Tested Not Optimized
#ffmpeg -i ./"$f" -vcodec h264 -acodec aac ./"${f%.MOV}.mp4"
#ffmpeg -i ./"$f" -vcodec h264 -acodec aac -strict -2 ./"${f%.MOV}.mp4"
#ffmpeg -i ./"$f" -c:v libx264 -crf 22 -preset slow -c:a aac -strict experimental -b:a 192k -ac 2 ./"${f%.MOV}.mp4"
#-vf "movie=/root/Convertion-Tool/watermark.png [watermark]; [in][watermark] overlay=main_w-overlay_w-10:10 [out]"

# Tested Optimized
#ffmpeg -i ./"$f" -vcodec h264 -crf 25 -vf scale=960:540 -acodec aac -strict -2 ./"${f%.MOV}.mp4"
}}}

== The Watermark ==

- Use below watermark so that the tool will automatically watermark any converted videos.

- Its Important to keep the watermark with the same name, and in the same directory with the script being used.

{{attachment:watermark.png}}


= Auto Scan & Auto Delete Anti-Virus Feature =

- Create the following Script and add to Crontab.

{{{

 /bin/bash   <-- Your Parser, precede with "!#"

cd /root/Anti-Virus/

echo
#### Emtpy the old scanlog and do a virus scan
rm -R /root/Anti-Virus/clamav-scan.log
touch /root/Anti-Virus/clamav-scan.log
clamdscan /Media-Content/ --fdpass --log=/root/Anti-Virus/clamav-scan.log --infected --multiscan

#### Delete Infected
echo
echo -en " Purging Infections if any found..." \\r

cat clamav-scan.log | grep /Media-Content/ | cut -d ":" -f1 > clamav-scan.purge

while read virus; do
rm "$virus"
done < clamav-scan.purge
sleep 1
cd /Media-Content/
rm *.tmp
echo -en ' Purging Infections if any found...[OK]' \\r
echo
echo "     >> ALL CLEAN ONCE AGAIN << "
echo

}}}

= In Operation =

- The Media Server has been built @192.168.0.73

- A Local only DNS record has been created for it :
 
streamer.noor.net
