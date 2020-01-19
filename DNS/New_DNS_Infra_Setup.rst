{{{
Author       : Ahmed AbdelSattaar
Version      : 1.1
Creation date: 2014-10-27
Latest update: 2014-10-27
}}}
<<TableOfContents(5)>>

== Overview ==
 . The old NOOR infrastructure consists of  two public DNS servers which are DNS1.noor.net (217.139.0.38) and DNS0.noor.net based on old bind version which not include Some important features like DNSSEC, rate limiting…etc, also the two DNS’s were containing all customers zones and  publicly accessible which make them prone to attack.

== New DNS Infrastructure ==
 . We have built a new DNS infrastructure and configured 4 DNS servers:-


 . {{attachment:DNS-Infra.jpg}}

=== DNS Master Setup ===

 * Host master DNS zones.
 * Managed through control panel and automatically create new DNS zones and update DNS records at slave DNS servers.
 * Configured with private interface at the Backend for security wise ,so it will update other DNS servers via this private network .
 * dnsmaster.noor.net backend --> eth0: 172.17.13.10 / 24  G.W:172.17.13.1


- Install Ubuntu 14 server with the following configuration
{{{
- hostname: dnsmaster , domain :noor.net
- IP address: 172.17.13.10 / 24  G.W:172.17.13.1
}}}
- Then run commands
{{{
	# apt-get install openssh-server
	# apt-get install vim nano
	# apt-get update
	# apt-get upgrade
}}}

- Edit Network Interface
{{{
# vi /etc/network/interfaces
iface eth0 inet static
        address 172.17.13.10
        netmask 255.255.255.0
        gateway 172.17.13.1
        # dns-* options are implemented by the resolvconf package, if installed
        dns-nameservers 127.0.0.1

}}}

- Edit hostname
 
{{{
# vi /etc/hostname
dnsmaster
}}}

{{{
# vi /etc/hosts
127.0.0.1      	 localhost.localdomain   localhost
172.17.13.10    dnsmaster.noor.net    dnsmaster
}}}

{{{
# hostname -f
dnsmaster.noor.net
}}}

- Adjust Time:
{{{
# dpkg-reconfigure tzdata
}}}

- Install DNS Server

{{{
# apt-get -y install bind9
}}}

- Configure Bind to listen only on IPV4
{{{
# vi /etc/default/bind9
# run resolvconf?
RESOLVCONF=no
# startup options for the server
#OPTIONS="-u bind"
OPTIONS="-4 -u bind"
}}}

- Edit Bind configuration file
{{{
# vi /etc/bind/named.conf.options
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
        
        version "not currently available :p";
        //forwarders { 8.8.8.8; 217.139.0.38;};

        recursive-clients 5000;

        notify yes;
        also-notify {
                172.17.13.38;
                172.17.20.118;
                };

        allow-transfer {
                172.17.13.38;
                172.17.20.118;
                };

        //allow-update { 217.139.225.250; } ;
        //allow-query { localhost; 192.168.0.0/16; };

        allow-recursion { localhost; 217.139.0.0/16; 41.187.0.0/16; 197.246.0.0/16; 10.0.0.0/8; 196.223.16.0/23; 84.205.96.0/19;};

        zone-statistics yes;
        statistics-file "/var/log/named/named_stats.txt";

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================

        //dnssec-validation auto;

        dnssec-validation no;

        auth-nxdomain no;    # conform to RFC1035
        //listen-on-v6 { any; };
        
};

logging {

    channel named_log {
         file "/var/log/named/named.log" versions 5 size 20m;

        severity debug 3;
        // severity info;
        //severity notice;
        print-category yes;
        print-severity yes;
        print-time yes;
    };

    category default { named_log; };
    category notify { named_log; };
    category resolver { named_log; };
    category security { named_log; };
    category queries { named_log; };

//old log at DNS0.noor.net
    category "general" { "named_log"; };
    category "database" { "named_log"; };
    category "config" { "named_log"; };
    category "xfer-in" { "named_log"; };
    category "xfer-out" { "named_log"; };
    category "client" { "named_log"; };
    category "unmatched" { "named_log"; };
    category "network" { "named_log"; };
    category "update" { "named_log"; };
    category "dispatch" { "named_log"; };
    category "dnssec" { "named_log"; };
  // category "lame-servers" { "named_log"; };
};

}}}

- Create Logs Files and Directories

{{{
# mkdir /var/log/named
# touch /var/log/named/named_stats.txt
# touch /var/log/named/named.log
# chown -R bind:root /etc/bind
# chmod -R 774 /etc/bind
}}}

- Configure Logs Files Rotation
{{{
 # vi /etc/logrotate.d/bind
/var/log/named/named.log {
   su root
    daily
    rotate 5
    compress
    delaycompress
    missingok
    notifempty
    create 0664 bind root
    postrotate
      /etc/init.d/bind9 reload > /dev/null
    endscript

}

/var/log/named/named_stats.txt {
   su root
    weekly
    rotate 5
    compress
    delaycompress
    missingok
    notifempty
    create 0664 bind root
    postrotate
      /etc/init.d/bind9 reload > /dev/null
    endscript

}

}}}

- Check Log Rotation 
{{{
# logrotate -fd /etc/logrotate.d/bind
}}}

- Restart Bind Service
{{{
# /etc/init.d/bind9 restart
}}}

- Install Webmin
{{{
# echo "deb http://download.webmin.com/download/repository sarge contrib" | sudo tee -a /etc/apt/sources.list
# echo "deb http://webmin.mirror.somersettechsolutions.co.uk/repository sarge contrib" | sudo tee -a /etc/apt/sources.list
# cd /tmp; wget http://www.webmin.com/jcameron-key.asc; sudo apt-key add jcameron-key.asc; cd
# sudo apt-get update
# sudo apt-get install webmin
}}}

- Loging to webmin using web browser with the following URL
{{{
https://172.17.13.10:10000

}}}

- Edit the following webmin setting 
{{{
Edit Webmin --> Under Servers --> Bind DNS Server --> Module Config
Module Config : 
	Add new zones to file :				/etc/bind/named.conf.local
	Add other slave IP addresses to list of masters on slaves? : No
	Directory for master zone files : 			/var/lib/bind
	Directory for slave/stub zone files : 			/var/lib/bind
	Default master server for master zones : 		dns1.noor.net (so the SOA NS will be dns1.noor.net)
	Serial number style Running :				Date based (YYYYMMDDnn) 
Servers -- > Bind --> Zone Defaults --> E-mail : dnsmgnt@noorgroup.net
			Default nameserver for master domains :  dns1.noor.net (so the SOA NS will be dns1.noor.net)
}}}

- Disable Apparmor
{{{
#service apparmor stop
#update-rc.d -f apparmor remove
#apt-get remove apparmor apparmor-utils
}}}
=== DNS1.noor.net (Slave1) ===

 * Host slave DNS zones, configured as read only DNS zones (binary files) not writable or editable.
 * Managed through control panel , so it will be updated and receive notification for any DNS changed from DNS master.
 * Configured with private interface at the Backend for update and receive notification from DNS master.
 * Configured with another public interface at Fronend and allowed only DNS service for DNS queries only.
 * dns1.noor.net                      : backend --> eth1: 172.17.13.38    frontend --> eth0: 217.139.0.38


- Install Ubuntu 14 server with the following configuration
{{{
- hostname: dns1 , domain :noor.net
- VM will have two NIC (Private - Internet)
- IP address: 172.17.13.38/ 24  G.W:172.17.13.1
- IP address: 217.139.0.38 / 29  G.W:217.139.0.33
}}}
- Then run commands
{{{
	# apt-get install openssh-server
	# apt-get install vim nano
	# apt-get update
	# apt-get upgrade
}}}

- Edit Network Interface
{{{
# vi /etc/network/interfaces
auto eth0
#iface eth0 inet dhcp
iface eth0 inet static
        address 217.139.0.38
        netmask 255.255.255.224
        gateway 217.139.0.33
        dns-nameservers 127.0.0.1
        # dns-* options are implemented by the resolvconf package, if installed

# BackEnd to have DNS Zone Transfer Locally
auto eth1
iface eth1 inet static
        address 172.17.13.38
        netmask 255.255.255.0
}}}

- Edit hostname
 
{{{
# vi /etc/hostname
dns1
}}}

{{{
# vi /etc/hosts
127.0.0.1       localhost.localdomain   localhost
172.17.13.38    dns1.noor.net   dns1
217.139.0.38    dns1.noor.net   dns1
}}}

{{{
# hostname -f
dns1.noor.net
}}}

- Adjust Time:
{{{
# dpkg-reconfigure tzdata
}}}

- Install DNS Server

{{{
# apt-get -y install bind9
}}}

- Configure Bind to listen only on IPV4
{{{
# vi /etc/default/bind9
# run resolvconf?
RESOLVCONF=no
# startup options for the server
#OPTIONS="-u bind"
OPTIONS="-4 -u bind"
}}}

- Edit Bind configuration file
{{{
# vi /etc/bind/named.conf.options
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
//      masterfile-format text;
        version "not currently available :p";
        //forwarders { 8.8.8.8; 217.139.0.38;};

        recursive-clients 5000;

        //notify yes;
        //also-notify { 217.139.225.250; };  // add all slave zone servers
        //allow-transfer { 217.139.225.250; };

        //allow-update { 217.139.225.250; } ;
        //allow-query { localhost; 192.168.0.0/16; };
        //allow-recursion { localhost; 192.168.0.0/16; };
        
        allow-recursion { localhost; 217.139.0.0/16; 41.187.0.0/16; 197.246.0.0/16; 10.0.0.0/8; 196.223.16.0/23; 84.205.96.0/19;};

        zone-statistics yes;
        statistics-file "/var/log/named/named_stats.txt";

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        dnssec-validation no;

        auth-nxdomain no;    # conform to RFC1035
        //listen-on-v6 { any; };
        //also-notify {};
};

// Added by NOOR

logging {

channel named_log {
         file "/var/log/named/named.log" versions 5 size 20m;

        //severity info ;
       //severity debug 3;
        severity notice;
        //severity warning;
        print-category yes;
        print-severity yes;
        print-time yes;
    };

        category default { named_log; };
        category notify { named_log; };
        category resolver { named_log; };
        category security { named_log; };
        category queries { named_log; };

//old log at DNS0.noor.net
    category "general" { "named_log"; };
    category "database" { "named_log"; };
    category "config" { "named_log"; };
    category "xfer-in" { "named_log"; };
    category "xfer-out" { "named_log"; };
    category "client" { "named_log"; };
   // category "unmatched" { "named_log"; };
    category "network" { "named_log"; };
    category "update" { "named_log"; };
   // category "dispatch" { "named_log"; };
    //category "dnssec" { "named_log"; };
    //category "lame-servers" { "named_log"; };
};
}}}

- Create Logs Files and Directories

{{{
# mkdir /var/log/named
# touch /var/log/named/named_stats.txt
# touch /var/log/named/named.log
# chown -R bind:root /etc/bind
# chmod -R 774 /etc/bind
}}}

- Configure Logs Files Rotation
{{{
 # vi /etc/logrotate.d/bind
/var/log/named/named.log {
   su root
    daily
    rotate 5
    compress
    delaycompress
    missingok
    notifempty
    create 0664 bind root
    postrotate
      /etc/init.d/bind9 reload > /dev/null
    endscript

}

/var/log/named/named_stats.txt {
   su root
    weekly
    rotate 5
    compress
    delaycompress
    missingok
    notifempty
    create 0664 bind root
    postrotate
      /etc/init.d/bind9 reload > /dev/null
    endscript

}

}}}

- Check Log Rotation 
{{{
# logrotate -fd /etc/logrotate.d/bind
}}}

- Restart Bind Service
{{{
# /etc/init.d/bind9 restart
}}}
- Install Webmin
{{{
# echo "deb http://download.webmin.com/download/repository sarge contrib" | sudo tee -a /etc/apt/sources.list
# echo "deb http://webmin.mirror.somersettechsolutions.co.uk/repository sarge contrib" | sudo tee -a /etc/apt/sources.list
# cd /tmp; wget http://www.webmin.com/jcameron-key.asc; sudo apt-key add jcameron-key.asc; cd
# sudo apt-get update
# sudo apt-get install webmin
}}}

- Edit the following webmin setting 
{{{
Edit Webmin --> Under Servers --> Bind DNS Server --> Module Config
Module Config : 
	Add new zones to file :				/etc/bind/named.conf.local
	Add other slave IP addresses to list of masters on slaves? : No
	Directory for master zone files : 			/var/lib/bind
	Directory for slave/stub zone files : 			/var/lib/bind
	Default master server for master zones : 		dns1.noor.net (so the SOA NS will be dns1.noor.net)
	Serial number style Running :				Date based (YYYYMMDDnn) 
Servers -- > Bind --> Zone Defaults --> E-mail : dnsmgnt@noorgroup.net
			Default nameserver for master domains :  dns1.noor.net (so the SOA NS will be dns1.noor.net)
}}}

- Disable Apparmor
{{{
#service apparmor stop
#update-rc.d -f apparmor remove
#apt-get remove apparmor apparmor-utils
}}}

=== DNS0.noor.net (Slave2) ===

 * Host slave DNS zones, configured as read only DNS zones (binary files) not writable or editable.
 * Managed through control panel , so it will be updated and receive notification for any DNS changed from DNS master.
 * Configured with private interface at the Backend for update and receive notification from DNS master.
 * Configured with another public interface at Fronend and allowed only DNS service for DNS queries only.
 * dns0.noor.net                      : backend --> eth1: 172.17.20.118  frontend --> eth0: 217.139.225.250


- Install Ubuntu 14 server with the following configuration
{{{
- hostname: dns0 , domain :noor.net
- VM will have two NIC (Private - Internet)
- IP address: 172.17.20.118 / 30  G.W:172.17.20.117
- IP address: 217.139.225.250 / 30  G.W:217.139.225.249
}}}
- Then run commands
{{{
	# apt-get install openssh-server
	# apt-get install vim nano
	# apt-get update
	# apt-get upgrade
}}}

- Edit Network Interface
{{{
# vi /etc/network/interfaces
auto eth0
#iface eth0 inet dhcp
iface eth0 inet static
        address 217.139.225.250
        netmask 255.255.255.252
        gateway 217.139..225.249
        dns-nameservers 127.0.0.1
        # dns-* options are implemented by the resolvconf package, if installed

# BackEnd to have DNS Zone Transfer Locally
auto eth1
iface eth1 inet static
        address 172.17.20.118
        netmask 255.255.255.0
#static route
#up route add -net 217.139.224.0/24 gw 172.17.20.118 dev eth1
up route add -net 172.17.0.0/16 gw 172.17.20.118 dev eth1
#up route add -net 192.168.0.0/16 gw 172.17.20.118 dev eth1

}}}

- Edit hostname
 
{{{
# vi /etc/hostname
dns1
}}}

{{{
# vi /etc/hosts 
127.0.0.1      	 localhost.localdomain   localhost
172.17.20.118  dns1.noor.net    dns1
217.139.225.250  dns1.noor.net    dns1

}}}

{{{
# hostname -f
dns0.noor.net
}}}

- Adjust Time:
{{{
# dpkg-reconfigure tzdata
}}}

- Install DNS Server

{{{
# apt-get -y install bind9
}}}

- Configure Bind to listen only on IPV4
{{{
# vi /etc/default/bind9
# run resolvconf?
RESOLVCONF=no
# startup options for the server
#OPTIONS="-u bind"
OPTIONS="-4 -u bind"
}}}

- Edit Bind configuration file
{{{
# vi /etc/bind/named.conf.options
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
//      masterfile-format text;
        version "not currently available :p";
        //forwarders { 8.8.8.8; 217.139.0.38;};

        recursive-clients 5000;

        //notify yes;
        //also-notify { 217.139.225.250; };  // add all slave zone servers
        //allow-transfer { 217.139.225.250; };

        //allow-update { 217.139.225.250; } ;
        //allow-query { localhost; 192.168.0.0/16; };
        //allow-recursion { localhost; 192.168.0.0/16; };
        
        allow-recursion { localhost; 217.139.0.0/16; 41.187.0.0/16; 197.246.0.0/16; 10.0.0.0/8; 196.223.16.0/23; 84.205.96.0/19;};

        zone-statistics yes;
        statistics-file "/var/log/named/named_stats.txt";

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        dnssec-validation no;

        auth-nxdomain no;    # conform to RFC1035
        //listen-on-v6 { any; };
        //also-notify {};
};

// Added by NOOR

logging {

channel named_log {
         file "/var/log/named/named.log" versions 5 size 20m;

        //severity info ;
       //severity debug 3;
        severity notice;
        //severity warning;
        print-category yes;
        print-severity yes;
        print-time yes;
    };

        category default { named_log; };
        category notify { named_log; };
        category resolver { named_log; };
        category security { named_log; };
        category queries { named_log; };

//old log at DNS0.noor.net
    category "general" { "named_log"; };
    category "database" { "named_log"; };
    category "config" { "named_log"; };
    category "xfer-in" { "named_log"; };
    category "xfer-out" { "named_log"; };
    category "client" { "named_log"; };
   // category "unmatched" { "named_log"; };
    category "network" { "named_log"; };
    category "update" { "named_log"; };
   // category "dispatch" { "named_log"; };
    //category "dnssec" { "named_log"; };
    //category "lame-servers" { "named_log"; };
};

}}}
- Create Logs Files and Directories

{{{
# mkdir /var/log/named
# touch /var/log/named/named_stats.txt
# touch /var/log/named/named.log
# chown -R bind:root /etc/bind
# chmod -R 774 /etc/bind
}}}

- Configure Logs Files Rotation
{{{
 # vi /etc/logrotate.d/bind
/var/log/named/named.log {
   su root
    daily
    rotate 5
    compress
    delaycompress
    missingok
    notifempty
    create 0664 bind root
    postrotate
      /etc/init.d/bind9 reload > /dev/null
    endscript

}

/var/log/named/named_stats.txt {
   su root
    weekly
    rotate 5
    compress
    delaycompress
    missingok
    notifempty
    create 0664 bind root
    postrotate
      /etc/init.d/bind9 reload > /dev/null
    endscript

}

}}}

- Check Log Rotation 
{{{
# logrotate -fd /etc/logrotate.d/bind
}}}

- Install Webmin
{{{
# echo "deb http://download.webmin.com/download/repository sarge contrib" | sudo tee -a /etc/apt/sources.list
# echo "deb http://webmin.mirror.somersettechsolutions.co.uk/repository sarge contrib" | sudo tee -a /etc/apt/sources.list
# cd /tmp; wget http://www.webmin.com/jcameron-key.asc; sudo apt-key add jcameron-key.asc; cd
# sudo apt-get update
# sudo apt-get install webmin
}}}

- Loging to webmin using web browser with the following URL
{{{
https://172.17.20.118:10000

}}}
- Edit the following webmin setting 
{{{
Edit Webmin --> Under Servers --> Bind DNS Server --> Module Config
Module Config : 
	Add new zones to file :				/etc/bind/named.conf.local
	Add other slave IP addresses to list of masters on slaves? : No
	Directory for master zone files : 			/var/lib/bind
	Directory for slave/stub zone files : 			/var/lib/bind
	Default master server for master zones : 		dns1.noor.net (so the SOA NS will be dns1.noor.net)
	Serial number style Running :				Date based (YYYYMMDDnn) 
Servers -- > Bind --> Zone Defaults --> E-mail : dnsmgnt@noorgroup.net
			Default nameserver for master domains :  dns1.noor.net (so the SOA NS will be dns1.noor.net)
}}}

- Disable Apparmor
{{{
#service apparmor stop
#update-rc.d -f apparmor remove
#apt-get remove apparmor apparmor-utils
}}}
=== Webmine :Configure DNS1 & DNS0 as Slave for master server ===

- Loging to webmin using web browser with the following URL
{{{
https://172.17.13.10:10000

}}}
- Then Edit the following webmin setting
{{{
Webmin Servers Index --> Scan for Webmin Servers
}}}

{{{
Broadcast for server : Broadcasting for servers on addresses 172.17.255.255 , 255.255.255.255 , 172.17.13.255 ..

Found this server at https://172.17.13.10:10000/

Found new server at https://172.17.13.38:10000/
Found new server at https://172.17.20.118:10000/
}}}

- Press "Edit" for two servers (172.17.13.38 - 172.17.20.118)
{{{
Server details
Hostname or IP address 	
Port 	
Server type 	:Ubuntu Linux
SSL server? 	: Yes
Description 	From hostname 
Member of server groups 	New group
Link type 	Normal link to server :	Login via Webmin with username (root/password)
Save.
}}}

- Under Servers Edit the following setting
{{{
Servers -->  Bind -->  Cluster Slave Servers
}}}

{{{
Add server 

172.17.13.38
-----------------	
View on slaves to add zones to 	: At top level

Create secondary on slave when creating locally? 	: Yes
Create all existing master zones on slave? 	:yes
Name for NS record 	: dns1.noor.net (this is the hostname for first NS which will added to every zone)

Press: Add
}}}

{{{
Add server 
172.17.20.118
------------------	
View on slaves to add zones to 	: At top level

Create secondary on slave when creating locally? 	: Yes
Create all existing master zones on slave? 	:yes
Name for NS record 	: dns0.noor.net (this is the hostname for second NS which will added to every zone)

Press: Add
}}}

=== DNS Mirror ===

 * Configured as Backup server for DNS Master, DNS1 and DNS0 (Backup DNS configuration files & DNS zones).

 * Managed through control panel (the same configurations of DNS Master).
 * Configured with private interface at the Backend for security wise.
 * dnsmirror.noor.net backend --> eth0: 172.17.13.11

-  Configure new DNS server same as master server with configurations :

{{{
- IP address: 172.17.13.11
- Hostname : dnsmirror.noor.net
- Webmin same as master server. (You can Export dnsmaster.noor.net then only change its Ip address)
}}}

- Create Backup script for dns master
{{{
# vi /usr/bin/dnsmaster-backup.sh
# Create backup directory with today date
date=$(date +"%Y-%m-%d_%k:%M")

source=172.17.13.10
user=administrator
hostname=dnsmaster.noor.net
destination=/backup/$hostname/$date/

mkdir -p $destination
mkdir -p $destination/zones


# synch bind config and zone files
rsync -avz ${user}@$source:/etc/bind  $destination
rsync -avz ${user}@$source:/var/lib/bind/* $destination/zones

# stop bind and webmin services
/etc/init.d/bind9 stop
/etc/init.d/webmin stop

# synch backuped data to bind directories
rsync -avz  $destination/bind/* /etc/bind/
rsync -avz  $destination/zones/* /var/lib/bind/

# start bind and webmin services
/etc/init.d/bind9 start
/etc/init.d/webmin start
}}}

- Create Backup script for dns0
{{{
# vi /usr/bin/dns0.noor.net-backup.sh 
--------------
# Create backup directory with today date
date=$(date +"%Y-%m-%d")

source=172.17.20.118
user=administrator
hostname=dns0.noor.net
destination=/backup/$hostname/$date/

mkdir -p $destination
mkdir -p $destination/zones


# synch bind config and zone files
rsync -avz ${user}@$source:/etc/bind  $destination
rsync -avz ${user}@$source:/var/lib/bind/* $destination/zones
}}}

- Create Backup script for dns1

{{{
# vi /usr/bin/dns1.noor.net-backup.sh 
--------------
# Create backup directory with today date
date=$(date +"%Y-%m-%d")

source=172.17.13.38
user=administrator
hostname=dns1.noor.net
destination=/backup/$hostname/$date/

mkdir -p $destination
mkdir -p $destination/zones


# synch bind config and zone files
rsync -avz ${user}@$source:/etc/bind  $destination
rsync -avz ${user}@$source:/var/lib/bind/* $destination/zones
-------------
}}}
- make script executable:
{{{
# chmod +x /usr/bin/dnsmaster-backup.sh
# chmod +x /usr/bin/dns0.noor.net-backup.sh
# chmod +x /usr/bin/dns1.noor.net-backup.sh
}}}

- Create and Copy public key from dns mirror to dns master
{{{
# ssh-keygen -t rsa
# scp /root/.ssh/id_rsa.pub administrator@172.17.13.10:/tmp/
}}}

- Login to dns master then run
{{{
# cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
# chmod 700  ~/.ssh/authorized_keys
}}}

- Test scp without password
{{{
scp /root/.ssh/id_rsa.pub administrator@172.17.13.10:/tmp/
}}}


- Create and Copy public key from dns mirror to dns0
{{{
# ssh-keygen -t rsa
# scp /root/.ssh/id_rsa.pub administrator@172.17.20.17:/tmp/
}}}

- Login to dns0 then run
{{{
# cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
# chmod 700  ~/.ssh/authorized_keys
}}}

- Test scp without password
{{{
scp /root/.ssh/id_rsa.pub administrator@172.17.20.17:/tmp/
}}}



- Create and Copy public key from dns mirror to dns1
{{{
# ssh-keygen -t rsa
# scp /root/.ssh/id_rsa.pub administrator@172.17.13.18:/tmp/
}}}

- Login to dns master then run
{{{
# cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
# chmod 700  ~/.ssh/authorized_keys
}}}

- Test scp without password
{{{
scp /root/.ssh/id_rsa.pub administrator@172.17.13.18:/tmp/
}}}



- Login to dns mirror and then test Backup Script 
{{{
root@dnsmirror # dnsmaster-backup.sh
root@dnsmirror # dns0.noor.net-backup.sh 
root@dnsmirror # dns1.noor.net-backup.sh 

}}}


- Add backup script to crontab
{{{
# crontab -e
# m h  dom mon dow   command
0 7,19 * * * /usr/bin/dnsmaster-backup.sh
0 0 * * 5 /usr/bin/dns1.noor.net-backup.sh
0 1 * * 5 /usr/bin/dns0.noor.net-backup.sh
}}}
