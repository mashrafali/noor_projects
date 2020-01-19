{{{
Author       : Mohamed Ashraf
Title        : NOORTube Server
Version      : 1.0
Creation date: 25/03/2015
Latest update: 25/03/2015
}}}

<<TableOfContents(4)>>


{{attachment:logo1.jpg}}




= Introduction =

- Since our need for a youtube like server, in order to store and instantly stream on demand whatever media content we have in NOOR (Ex: Chalk Talks)

- A Tube Streaming Server - based on MediaDrop - has been built and configured for this purpose.

- A FFMPEG converter has been also built for any necessary uses.

= Features =

'''''- The system is capable of the following: '''''

1- Storing and organizing our Media Files based on categories.

2- Streaming most used video/audio formats (Preferably MP3, MP4 & FLV Formats).

3- Local converter tool for almost any to any format conversion.

4- Ability to view media popularity and number of viewing.

5- Users commenting, likes and dislikes.

6- Users can upload their own media to get it published (After admin approval on the media content).

7- Ability to import Videos from Youtube.

..and alot more

= Streaming Capabilities =

'''''- The User can stream videos using any of the following methods: '''''

1- Stream directly via WEB-UI.

2- Stream on Android/Apple Smart Phones. 

= System Built =

- An '''Auto-Deploy Script''' has been created which aggregates all necessary steps for system installation.

- To save any future Deployments time, copy over the below script and run as ''root''.

'''''NOORTube-Auto-Deploy.sh'''''
{{{

 /bin/bash   <-- Your Parser, precede with "!#"

############## RUN AS ROOT
MYIP=$(ifconfig eth0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo "                                            NOOR Tube System Auto Deployment  "
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
apt-get install ssh htop pydf unzip iftop snmpd snmp make nano tcpdump gcc sudo dnsutils ntp -y
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get autoremove -y
apt-get autoclean
echo
echo
echo " #################################### INSTALLING ACTIVE ANTI-VIRUS ############################"
sleep 2
echo
echo "Installing ClamAV Software..."
echo
sleep 1
apt-get install clamav-daemon -y
echo
echo "Installing ClamAV Software...[OK]"
echo
echo "Updating Definitions..."
echo
sleep 1
freshclam
echo
echo "Updating Definitions...[OK]"
echo
echo "Registering services..."
echo
/etc/init.d/clamav-daemon start
echo
sleep 1
echo "Registering services...[OK]"
echo
echo
echo " #################################### INSTALLING VM-WARE TOOLS ############################"
sleep 2
mkdir /mnt/vm-tools
mount /dev/sr0 /mnt/vm-tools
mkdir /root/vm-tools
cp /mnt/vm-tools/VMwareTools-* /root/vm-tools
cd /root/vm-tools/
tar zxvf VMwareTools-*
echo
echo
echo " #################################### CONFIGURE VM-WARE TOOLS ############################"
echo
echo
/root/vm-tools/vmware-tools-distrib/vmware-install.pl --default
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
echo " #################################### Building NOOR Tube System  ############################"
echo
echo "Installing Dependencies..."
sleep 1
echo
apt-get install mysql-server mysql-client -y
apt-get install libjpeg-dev zlib1g-dev libfreetype6-dev libmysqlclient-dev python-dev -y
apt-get install python-setuptools python-virtualenv -y
echo
echo "Installing Dependencies...[OK]"
echo
echo "Installing Web Server..."
sleep 1
echo
apt-get install apache2 -y
echo
echo "Installing Web Server...[OK]"
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
        RewriteRule ^/(.*) http://streamer.noor.net:8080/$1 [P]
</VirtualHost> ' > /etc/apache2/sites-available/000-default.conf
echo "[OK]"
echo
echo "Creating Python Virtual Environment..."
sleep 1
echo
virtualenv --distribute --no-site-packages venv
echo
echo "Creating Python Virtual Environment...[OK]"
echo
echo "Activating Virtual Environment..."
sleep 1
#source venv/bin/activate
echo "[OK]"
echo
echo "Installing System Core..."
sleep 1
echo
mkdir tube-core
cd tube-core/
wget http://static.mediadrop.net/releases/MediaCore-0.10.3.tar.gz
tar xzvf MediaCore-0.10.3.tar.gz
cd MediaCore-0.10.3/
python setup.py develop
cd ..
echo
echo "Installing System Core...[OK]"
echo
echo "Generating Configuration File..."
sleep 1
echo
paster make-config MediaCore deployment.ini
echo
echo "Generating Configuration File...[OK]"
echo
echo "Generating Data Directory..."
sleep 1
echo
cp -a MediaCore-0.10.3/data .
echo
echo "Generating Data Directory...[OK]"
echo
echo "    !!!! NOTE: For uploads to work, the data directory must be writable by the user running MediaDrop. !!!!"
sleep 5
echo

#echo "Loading Initial Parameters..."
#sleep 1
#echo
#paster setup-app deployment.ini
#echo
#echo "Loading Initial Parameters...[OK]"
#echo

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

= Auto Startup & Initialization =

- For the system to start its services upon startup, the following initialization script is created.

'''''@/etc/init.d/noor-tube'''''
{{{
# Start or stop NOOR-Tube Streaming Service


# By Mohamed Ashraf


### BEGIN INIT INFO


# Provides:          NOOR-Tube (Init-Script)

# Required-Start:    NOOR-Tube

# Required-Stop:     NOOR-Tube

# Short-Description: Start NOOR TUBE SERVICES

# Description:       Enable service provided by daemon.


### END INIT INFO
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
case "$1" in
start)
echo
echo "Starting NOOR TUBE Streaming Service..."

cd /root/tube-core
(paster serve --reload deployment.ini&)
sleep 2
echo "[OK]"
echo
sleep 1
;;

stop)
echo
echo -en "Stopping NOOR TUBE Streaming Service..."
sleep 2
pkill paster
echo "[OK]"
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
chmod a+x /etc/init.d/noor-tube
}}}

- Then initialize the Script to be run by the kernel at system Boot

{{{
update-rc.d noor-tube defaults
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

= Network Share Configuration =

- Samba has been used to share the folder over the network.

'''''smb.conf'''''
{{{
#=================  Global Settings =====================================
[global]
workgroup = NOOR
server string = Samba Server %v
netbios name = backup
security = user
map to guest = bad user
dns proxy = no

#============================ Share Definitions ==========================
[NOOR-TUBE]
    comment = NOOR-TUBE  
    path = /Media-Content/share/
    valid users = test
    browsable = yes
    guest ok = no
    read only = no
    create mask = 0755


#### CREDENTIALS:
# USER: test
# PASS: cisco123

#### User added with:
# smbpasswd -a test
# After adding the user into linux
}}}

= System Crontab =

- The following Jobs are assigned to system crontab

{{{
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed


# AUTOMATIC CONVERSION
0 */12 * * * /root/Convertion-Tool/convert-my-media.sh

# ACTIVE ANTI-VIRUS
#0 3 * * * /root/Anti-Virus/scan-and-delete.sh

# MOUNTING EXTERNAL HARD DRIVE
@reboot mount -t ntfs /dev/sdb1 /Media-Content/

#NOTE [FOR MAC TO SERVER SCP]
#scp -r test@192.168.35.152:/Users/test/Desktop/noor/NEW/orginal/ original/
}}}

= In Operation =

- The NoorTube Server has been built @192.168.0.73

- A Local only DNS record has been created for it :
 
noortube.net

= Application Administration =

- The system can be administered by visiting the admin url (http://<SERVER IP OR HOSTNAME>/admin)

{{attachment:Step0.jpg}}

== Uploading Media ==

1- first login into admin page:

{{attachment:Step1.jpg}}

2- Click on "Add New Media"

{{attachment:Step1.1.jpg}}

3- The Add Media Controls will open for you

{{attachment:step2.jpg}}

4- Click on "Add and Manage"

{{attachment:step2.1.jpg}}

5- Click on "Upload a File" if you have a local media file or use "Add URL" if you need to import a YOUTUBE Video Link.

{{attachment:Step3.jpg}}

6- Add the Following Meta Data Configuration:

A- Video Name

B- Video Description (Optional or make it same name as Video)

C- Video Category (Training or Chalk Talk or ...)


{{attachment:step3.1.jpg}}

7- Upload a Thumbnail for the video (A Video related Picture so your video view is more professional), you can use any picture you have or download it from google.

{{attachment:step3.2.jpg}}

8- Finally Click on Save & Publish the Video

{{attachment:step3.3.jpg}}


= Usage Analysis =

- Usage Parsing and analysis

- Just run analyze in server as in content of /usr/bin/analyze

{{{
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games

echo
echo -en "## Analyzing Raw Data [..................] 0%" \\r && sleep 0.1
cat /dev/null > /tmp/tube-activity
echo -en "## Analyzing Raw Data [##................] 11%" \\r && sleep 0.1
zcat /var/log/apache2/access.log.6.gz  | grep "http://noortube.net/media/" | grep "GET /files/" >> /tmp/tube-activity
echo -en "## Analyzing Raw Data [####..............] 22%" \\r && sleep 0.1
zcat /var/log/apache2/access.log.5.gz  | grep "http://noortube.net/media/" | grep "GET /files/" >> /tmp/tube-activity
echo -en "## Analyzing Raw Data [######............] 33%" \\r && sleep 0.1
zcat /var/log/apache2/access.log.4.gz  | grep "http://noortube.net/media/" | grep "GET /files/" >> /tmp/tube-activity
echo -en "## Analyzing Raw Data [########..........] 44%" \\r && sleep 0.1
zcat /var/log/apache2/access.log.3.gz  | grep "http://noortube.net/media/" | grep "GET /files/" >> /tmp/tube-activity
echo -en "## Analyzing Raw Data [##########........] 55%" \\r && sleep 0.1
zcat /var/log/apache2/access.log.2.gz  | grep "http://noortube.net/media/" | grep "GET /files/" >> /tmp/tube-activity
echo -en "## Analyzing Raw Data [############......] 66%" \\r && sleep 0.1
cat /var/log/apache2/access.log.1      | grep "http://noortube.net/media/" | grep "GET /files/" >> /tmp/tube-activity
echo -en "## Analyzing Raw Data [##############....] 77%" \\r && sleep 0.1
cat /var/log/apache2/access.log        | grep "http://noortube.net/media/" | grep "GET /files/" >> /tmp/tube-activity
echo -en "## Analyzing Raw Data [################..] 88%" \\r && sleep 0.1
cat /dev/null > /tmp/parsed-activity
cat /dev/null > /tmp/counting-uses
lastdate=$(tail -1 /tmp/tube-activity | awk {'print $4'} | cut -d "[" -f2)
lastvid=$(tail -1 /tmp/tube-activity  | awk 'BEGIN {FS="http://noortube.net/media/"} {print $2}' | cut -d '"' -f1)
echo -en "## Analyzing Raw Data [##################] 100%    - Success -" \\r && sleep 1
echo
echo


let progress=0
lines=$(cat /tmp/tube-activity | wc -l)
step=$(bc <<< "scale=3; 100/$lines")

while read usage
do
  user=$(echo $usage   | awk {'print $1'})
  using=$(echo $usage  | awk 'BEGIN {FS="http://noortube.net/media/"} {print $2}' | cut -d '"' -f1)
  when=$(echo $usage   | awk {'print $4'} | cut -d "[" -f2)
  echo "$user : $using  $when" >> /tmp/parsed-activity
  echo "$user" >> /tmp/counting-uses
  progress=$(bc <<< "scale=3; $progress+$step")
  echo -en "## Processing Data Please be patient.......$progress%" \\r
done < /tmp/tube-activity
echo -en "## Processing Data Please be patient.......100%    - Success -" \\r && sleep 1
echo
echo

echo -en "## Matching Users with Used Activity.......0%" \\r
sort /tmp/counting-uses | uniq -c > /tmp/Final-count
echo -en "## Matching Users with Used Activity.......10%" \\r
tac /tmp/parsed-activity | sort -k1,1 -r -u > /tmp/Final-Data
echo -en "## Matching Users with Used Activity.......20%" \\r
let progress=20
lines=$(cat /tmp/Final-count | wc -l)
step=$(bc <<< "scale=2; 80/$lines")
while read counting
do
   userc=$(echo $counting  | awk {'print $2'})
   countc=$(echo $counting | awk {'print $1'})
   sed -i "s#$userc#$counting#g" /tmp/Final-Data
   progress=$(bc <<< "scale=3; $progress+$step")
   echo -en "## Matching Users with Used Activity.......$progress%" \\r
   sleep 0.2
done < /tmp/Final-count
echo -en "## Matching Users with Used Activity.......100%    - Success -" \\r


echo "--------------------------------------------------------------------------------------------------------------" >  /tmp/results
echo "|    USER IP    |Request|                  Last Media Activity                        |      Date Viewed     |" >> /tmp/results
echo "--------------------------------------------------------------------------------------------------------------" >> /tmp/results

while read final
do
  count=$(echo $final  | awk {'print $1'})
  user=$(echo $final   | awk {'print $2'})
  using=$(echo $final  | awk {'print $4'})
  when=$(echo $final   | awk {'print $5'})
  printf "|%15s|%7s|%61s|%22s| %s\n" "$user " "$count  " "$using           " "$when "                                 >> /tmp/results
done < /tmp/Final-Data
echo "--------------------------------------------------------------------------------------------------------------" >> /tmp/results
echo                                                                                                                  >> /tmp/results
echo "Last Time  Content has been viewed : $lastdate "                                                                >> /tmp/results
echo "Last Video Content has been Viewed : $lastvid  "                                                                >> /tmp/results
echo
echo
echo
for i in {3..0}
do
echo -en "            -- Data arranged, viewing in $i seconds --" \\r
sleep 1
done
echo
echo
clear
cat /tmp/results
echo
echo
}}}
