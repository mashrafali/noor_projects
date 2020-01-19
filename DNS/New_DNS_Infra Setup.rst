{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-09-18
Latest update: 2014-09-18
}}}
<<TableOfContents(5)>>

== Overview ==
 . The old NOOR infrastructure consists of  two public DNS servers which are DNS1.noor.net (217.139.0.38) and DNS0.noor.net based on old bind version which not include Some important features like DNSSEC, rate limiting…etc, also the two DNS’s were containing all customers zones and  publicly accessible which make them prone to attack.

== New DNS Infrastructure ==
 . We have built a new DNS infrastructure and configured 4 DNS servers:-

 . {{attachment:DNS-Infra.jpg}}

__'''a- DNS Master'''__

 * Host master DNS zones.
 * Managed through control panel and automatically create new DNS zones and update DNS records at slave DNS servers.
 * Configured with private interface at the Backend for security wise ,so it will update other DNS servers via this private network .
 * dnsmaster.noor.net backend --> eth0: 172.17.13.10

__'''b- DNS Mirror'''__

 * Configured as Backup server for DNS Master, DNS1 and DNS0 (Backup DNS configuration files & DNS zones).

 * Managed through control panel (the same configurations of DNS Master).
 * Configured with private interface at the Backend for security wise.
 * dnsmirror.noor.net backend --> eth0: 172.17.13.11

__'''c- DNS1 & DNS0'''__

 * Host slave DNS zones, configured as read only DNS zones (binary files) not writable or editable.
 * Managed through control panel , so it will be updated and receive notification for any DNS changed from DNS master.
 * Configured with private interface at the Backend for update and receive notification from DNS master.
 * Configured with another public interface at Fronend and allowed only DNS service for DNS queries only.
 * dns1.noor.net                      : backend --> eth1: 172.17.13.38    frontend --> eth0: 217.139.0.38
 * dns0.noor.net                      : backend --> eth1: 172.17.20.118  frontend --> eth0: 217.139.225.250

== New DNS Features ==
 . by implementing the new DNS Infrastructure we will have the below features:

__'''a- DNS clustering'''__

 * Service stability and High availability.
 * DNS master isolation for Master DNS server.
 * Publishing  Read only DNS Servers  for public network.
 * Centralize control panel for DNS zones/ Record management.

'''__b- DDos attack prevention__'''

 * New features and security fixes in Bind-9.9.5
 * https://kb.isc.org/article/AA-01111/0/BIND-9.9.5-Release-Notes.html

__'''c- Last updated operating systems & DNS server'''__

__Old DNS Infra__

 * CentOS Linux 5.9
 * BIND version 9.3.6
 * Webmin 1.69

__New DNS Infra__

 * Ubuntu Server 14.04 LTS

 . New features in Ubuntu 14.04 https://wiki.ubuntu.com/TrustyTahr/ReleaseNotes

 * Bind DNS Server 9.9.5-3-Ubuntu (Extended Support Version)

 . New features in Bind DNS Server 9.9.5 https://kb.isc.org/article/AA-01111/0/BIND-9.9.5-Release-Notes.html

 * Webmin 1.7

 . New Features in Webmin 1.7 [[http://www.webmin.com/changes.html__'''d-|http://www.webmin.com/changes.html]]

__'''d- Monitoring DNS services '''__

'''__e- Statistical Analysis and data Mining __'''
