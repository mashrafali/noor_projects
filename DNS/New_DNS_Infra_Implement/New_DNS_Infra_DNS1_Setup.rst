{{{
Author       : Ahmed AbdelSattaar
Version      : 1.1
Creation date: 2014-10-27
Latest update: 2015-04-09
}}}
=== DNS0.noor.net (Slave2) ===

 * Host slave DNS zones, configured as read only DNS zones (binary files) not writable or editable.
 * Managed through control panel , so it will be updated and receive notification for any DNS changed from DNS master.
 * Configured with private interface at the Backend for update and receive notification from DNS master.
 * Configured with another public interface at Fronend and allowed only DNS service for DNS queries only.
 * dns0.noor.net                      : backend --> eth1: 172.17.30.118  frontend --> eth0: 217.139.225.250


- Install Ubuntu 14 server with the following configuration
{{{
- hostname: dns0 , domain :noor.net
- VM will have two NIC (Private - Internet)
- IP address: 172.17.30.118 / 30  G.W:172.17.20.117
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
        address 172.17.30.118
        netmask 255.255.255.0
#static route
#up route add -net 217.139.224.0/24 gw 172.17.30.118 dev eth1
up route add -net 172.17.0.0/16 gw 172.17.30.118 dev eth1
#up route add -net 192.168.0.0/16 gw 172.17.30.118 dev eth1

}}}

- Edit hostname
 
{{{
# vi /etc/hostname
dns1
}}}

{{{
# vi /etc/hosts 
127.0.0.1      	 localhost.localdomain   localhost
172.17.30.118  dns1.noor.net    dns1
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
https://172.17.30.118:10000

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

- Edit Apparmor for new bind configuration
{{{
#vi /etc/apparmor.d/usr.sbin.named
  /etc/bind/** r,
  /var/lib/bind/** lrw,
  /var/lib/bind/ rw,
  /var/cache/bind/** lrw,
  /var/cache/bind/ rw,
}}}

- Restart Apparmor Service
{{{
# /etc/init.d/apparmor restart
}}}

- Configure ufw Firewall
{{{
# ufw app list
Available applications:
  Bind9
  OpenSSH

# ufw enable

# ufw allow Bind9
# ufw allow from 172.17.13.10
# ufw allow from 172.17.13.11
# ufw allow proto any from 217.139.224.0/24 to any port 22
# ufw allow proto any from 217.139.224.0/24 to any port 10000
# ufw allow proto any from 217.139.224.0/24 to any port 443
# ufw allow proto any from 217.139.224.113 to any port 161
}}}

- Check ufw Rules

{{{
# ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] Bind9                      ALLOW IN    Anywhere
[ 2] Anywhere                   ALLOW IN    172.17.13.10
[ 3] Anywhere                   ALLOW IN    172.17.13.11
[ 4] 22                         ALLOW IN    217.139.224.0/24
[ 5] 10000                      ALLOW IN    217.139.224.0/24
[ 6] 443                        ALLOW IN    217.139.224.0/24
[ 7] Bind9 (v6)                 ALLOW IN    Anywhere (v6)

}}}
