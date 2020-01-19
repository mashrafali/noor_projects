## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Install_DNS_server
## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Install_DNS_server
## page was renamed from Enterprise/IT/Docs/How_To/Linux/Install_DNS_server
## page was renamed from Enterprise/IT/Docs/How_To/Install_DNS_server
{{{
Author       : Amr Mohsen
Version      : 1.1
Creation date: 2012-06-25
Latest update: 2012-06-26
}}}
<<TableOfContents(2)>>

= Install DNS server =
== How to install DNS Server ==
{{{
apt-get -y install bind9
}}}
== How to check DNS service ==
{{{
/etc/init.d/bind9 status
}}}
== How to add primary zone ==
{{{
vi /etc/bind/named.conf
}}}
the default content

{{{
# # # # # # # # # # # # # # # # # # # # #

// This is the primary configuration file for the BIND DNS server named.

//

// Please read /usr/share/doc/bind9/README.Debian.gz for information on the

// structure of BIND configuration files in Debian, *BEFORE* you customize

// this configuration file.

//

// If you are just adding zones, please do that in /etc/bind/named.conf.local

include "/etc/bind/named.conf.options";

include "/etc/bind/named.conf.local";

include "/etc/bind/named.conf.default-zones";

# # # # # # # # # # # # # # # # # # # # #
}}}
EX: for add primary zone Press i to edit

{{{
zone "noorgroup.net" {

  type master;
  file "/var/lib/bind/noorgroup.net.hosts";
  };
}}}
to save press Esc and write

{{{
:x
}}}
Restart the bind service

{{{
/etc/init.d/bind9  restart
}}}
That will create /var/lib/bind/noorgroup.net.hosts that for add records

== How to add record ==
{{{
vi /var/lib/bind/noorgroup.net.hosts
}}}
EX:

{{{
adsl2.noorgroup.net.    IN      A       172.17.13.12

pedigree.noorgroup.net. IN      A       172.17.13.14
}}}
== How to add secondary zone ==
{{{
vi /etc/bind/named.conf
}}}
EX:

{{{
zone "noor.net" {

  type slave;
  masters {
  192.168.0.2;
  };
 file "/var/lib/bind/noor.net.hosts"; };
}}}
== How to test the zone by dig command ==
{{{
dig www.noorgroup.net
}}}
the result:

{{{
; <<>> DiG 9.7.3 <<>> www.noorgroup.net

;; global options: +cmd

;; Got answer:

;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59007

;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

;; QUESTION SECTION:

;www.noorgroup.net.             IN      A

;; ANSWER SECTION:

www.noorgroup.net.      38400   IN      A       41.187.100.7

;; AUTHORITY SECTION:

noorgroup.net.          38400   IN      NS      DNS1.noor.net.

;; ADDITIONAL SECTION:

DNS1.noor.net.          3600    IN      A       192.168.0.4

;; Query time: 0 msec

;; SERVER: 192.168.0.4#53(192.168.0.4)

;; WHEN: Sun Jun 24 13:17:32 2012

;; MSG SIZE  rcvd: 30
}}}
== How to change bind options ==
{{{
vi /etc/bind/named.conf.options

options {

 directory "/var/cache/bind";
 // If there is a firewall between you and nameservers you want
 // to talk to, you may need to fix the firewall to allow multiple
 // ports to talk.  See http://www.kb.cert.org/vuls/id/800113
 // If your ISP provided one or more IP addresses for stable
 // nameservers, you probably want to use them as forwarders.
 // Uncomment the following block, and insert the addresses replacing
 // the all-0's placeholder.
 forwarders {
 217.139.0.38;
  };
  notify yes;
  allow-transfer {
  192.168.0.5;
  };
 allow-update { 192.168.0.5; } ;
 allow-query { localhost; 192.168.0.0/16; };
 allow-recursion { localhost; 192.168.0.0/16; };

};
}}}
== how to backup DNS ==
{{{
ssh amohsen@192.168.0.4

rsync -avz /var/lib/bind/ username@DST-ip-address:/dst-bkb-folder

rsync -avz /etc/bind/ username@DST-ip-address:/dst-bkb-folder
}}}
