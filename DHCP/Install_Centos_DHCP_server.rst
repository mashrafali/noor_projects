## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Install_DHCP_server
## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Install_DHCP_server
## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Install_DHCP_server
## page was renamed from Enterprise/IT/Docs/How_To/Linux/Install_DHCP_server
## page was renamed from Enterprise/IT/Docs/How_To/Install_DHCP_server
{{{
Author       : Amr Mohsen
Version      : 1.1
Creation date: 2012-06-19
Latest update: 2012-06-26
}}}



<<TableOfContents(2)>>

= Install DHCP server =

== How to install DHCP server ==

== How to configure DHCP server ==

Open the terminal
{{{
sudo yum install  -y dhcp
}}}
Now you must copy the source file
{{{
cp  /usr/share/doc/dhcp-3.0.5/dhcpd.conf.sample /etc/dhcpd.conf
}}}
open the file to edit
{{{
vi /etc/dhcpd.conf
}}}
the default content in the file
{{{
ddns-update-style interim;

ignore client-updates;

subnet 192.168.0.0 netmask 255.255.255.0 {

# --- default gateway

 . option routers                  192.168.0.1;
 . option subnet-mask              255.255.255.0;
 . option nis-domain               "domain.org";
 . option domain-name              "domain.org";
 . option domain-name-servers      192.168.1.1;
 . option time-offset              -18000; # Eastern Standard Time

#       option ntp-servers              192.168.1.1;

#       option netbios-name-servers     192.168.1.1;

# --- Selects point-to-point node (default is hybrid). Don't change this unless

# -- you understand Netbios very well

#       option netbios-node-type 2;

 . range dynamic-bootp 192.168.0.128 192.168.0.254;
 . default-lease-time 21600;
 . max-lease-time 43200;
 . # we want the nameserver to appear at a fixed address host ns {
  . next-server marvin.redhat.com;
  hardware ethernet 12:34:56:78:AB:CD; fixed-address 207.175.42.254;
 }

}
}}}
now edit to configure
{{{
vi /etc/dhcpd.conf
}}}
This our configuration and the brief explain beside it:
{{{
ddns-update-style none;

deny bootp;

authoritative;
}}} 
#we have  two subnet the first for corporate and the second for the voice (telephones)

#The corporate Ethernet in vlan (1) and the voice Ethernet in vlan (13)
{{{
subnet 192.168.0.0 netmask 255.255.255.0 {

 . interface eth0;
 . option routers 192.168.0.1;
 . option subnet-mask 255.255.255.0;
 . option domain-name "noor.net";
 . option domain-name-servers 192.168.0.4 , 192.168.0.5;

 . range 192.168.0.40 192.168.0.100;
 . range  192.168.0.105 192.168.0.254;
 . default-lease-time 259200;
 . max-lease-time 259200;
  .  host mnoman-core {
   . hardware ethernet 00:25:64:63:5d:31;
   . fixed-address 192.168.0.169;
   . }

subnet 192.168.13.0 netmask 255.255.255.0

{

interface eth1;

option routers  192.168.13.1;

option subnet-mask 255.255.255.0;

option domain-name "noor.net";

option domain-name-servers 192.168.0.4, 192.168.0.5;

range dynamic-bootp 192.168.13.10 192.168.13.254;

default-lease-time 259200;

max-lease-time 259200;

}
}}}
Now save and exit the file

Press ESC to go to command mode
{{{
:x to save and exit
}}}
Now restart the service and test
{{{
/etc/init.d/dhcpd restart
}}}
Connect any client with this server, it will pull ip address
