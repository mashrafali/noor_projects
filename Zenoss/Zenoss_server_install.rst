## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Zenoss_server
## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Zenoss_server
## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Zenoss server
{{{
Author       : Amr Mohsen
Version      : 1.1
Creation date: 2012-11-27
Latest update: 2012-11-27
}}}
<<TableOfContents(3)>>

== What is Zenoss monitoring tool Instal the server and update it ==
-Zenoss (Zenoss Core) is an open-source application, server, and network management platform based on the Zope application server. Released under the GNU General Public License (GPL) version 2, Zenoss Core provides a web interface that allows system administrators to monitor availability, inventory/configuration, performance, and events.

-Zenoss is today's premier open source IT management solution. Through integrated monitoring, it enables you to manage the status and health of your infrastructure through a single, Web-based console.
== How to connect ssh on the server ==
{{{
ssh username@IP-address
}}}
== How to update the server ==
{{{
yum update -y
}}}
== what is pre-request H/W ==
Deployments Up to 1000 Devices

Deployment   Size  -->   Memory  ,   CPU    ,    Storage

1 to 250    devices   -->  4GB    ,    2 cores ,  1x300GB, 10K RPM drive or SSD                            

250 to 500  devices   -->  8GB   ,     4 cores   , 1x300GB, 10K RPM drive or SSD                              

500 to 1000 devices   -->  16GB    ,   8 cores ,   1x300GB, 15K RPM drive or SSD
                                
== what is pre-request S/W ==
Prerequisite and the Version

Oracle Java 1.6 Update 31 or later. (1.7 is not supported.)

RRDtool 1.4.7 or later

MySQL Community Server 5.5.25 or later

RabbitMQ 2.8.4 or later

Nagios Plugins 1.4.15 or later

Erlang R12B

== Configure Your Firewall ==
Zenoss Core requires these ports be open in your firewall:
Port Protocol

11211 TCP | UDP Inbound memcached

8080 TCP Inbound Web interface

514 UDP Inbound syslog

162 UDP Inbound SNMP Traps

== Alternatively, you can choose to disable your firewall ==
For IPv4, use these commands:
{{{
service iptables stop
chkconfig iptables off
}}}
For IPv6, use these commands:
{{{
service ip6tables stop
chkconfig ip6tables off
}}}
== Install Oracle Java ==
Download Oracle JRE:
{{{
wget -O jre-6u31-linux-x64-rpm.bin \
http://javadl.sun.com/webapps/download/AutoDL?BundleId=59622
}}}
== Change mode: ==
{{{
chmod +x ./jre-6u31-linux-x64-rpm.bin
}}}
== Install Oracle JRE: ==
{{{
./jre-6u31-linux-x64-rpm.bin
}}}
== Update JAVA_HOME. Add the following line to the end of the /etc/profile file: ==
{{{
export JAVA_HOME=/usr/java/default
}}}
== Verify the correct installed version: ==
{{{
java -version
}}}
== Download and Install and Configure RRDtool ==
{{{
wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.2-2.el5.rf.x86_64.rpm
}}}
{{{
yum -y --nogpgcheck localinstall rpmforge-release-0.5.2-2.el5.rf.x86_64.rpm
}}}
== Disable automatic access to the RPMforge YUM repository: ==
{{{
vim /etc/yum.repos.d/rpmforge.repo
}}}
Change the value of "enabled" in the [rpmforge]  --> enabled = 0
== Install RRDtool: ==
{{{
yum -y --enablerepo=rpmforge install rrdtool-1.4.7
}}}
== Install and Configure MySQL server ==
from this site (http://dev.mysql.com/downloads/mysqld) 
download the latest version of this package 

• MySQL Client Utilities (currently MySQL-client-5.5.25a-1.rhel5.x86_64.rpm)

• MySQL Server (currently MySQL-server-5.5.25a-1.rhel5.x86_64.rpm)

• MySQL Shared (currently MySQL-shared-5.5.25a-1.rhel5.x86_64.rpm)

== Install the three RPMs with the command: ==
{{{
yum -y --nogpgcheck localinstall MySQL*
}}}
== Create a file named /etc/my.cnf file, and then add the following lines: ==
{{{
vim /etc/my.cnf
}}}
add this line
{{{
[mysqld]
max_allowed_packet=16M
innodb_buffer_pool_size=256M
innodb_additional_mem_pool_size=20M
}}}
==  Enable EPEL Repository Access ==
{{{
wget -r -l1 --no-parent -A 'epel*.rpm' http://dl.fedoraproject.org/pub/epel/5/x86_64/
}}}
== install the Repository ==
{{{
yum -y --nogpgcheck localinstall dl.fedoraproject.org/pub/epel/5/x86_64/epel-*.rpm
}}}
== Edit the /etc/yum.repos.d/epel.repo file. ==
Change the value of "enabled" in the [ epel ] stanza: --> enabled = 0

== Install RabbitMQ ==
Use this command to download, install, and configure RabbitMQ:
{{{
wget http://www.rabbitmq.com/releases/rabbitmq-server/v2.8.4/rabbitmq-server-2.8.4-1.noarch.rpm
}}}
{{{
yum -y --nogpgcheck --enablerepo=epel localinstall rabbitmq-server-2.8.4-1.noarch.rpm
}}}
== start the rabbitmq-server daemon ==
{{
service rabbitmq-server start
chkconfig rabbitmq-server on
}}}
== Install Zenoss Core ==
Download the Installation Files from this site --> http://community.zenoss.org/community/download
Install the RPM file:
{{{
yum -y --nogpgcheck --enablerepo=epel localinstall zenoss-Version.el5.x86_64.rpm
}}}
== Start and Configure memcached and snmpd ==
Use the commands to start and configure the memcached and snmpd daemons:
1. Start the memcached daemon and configure it to start automatically on reboot:
{{{
service memcached start
chkconfig memcached on
}}}
2. Start the snmpd daemon and configure it to start automatically on reboot:
{{{
service snmpd start
chkconfig snmpd on
}}}
== start Zenoss Core service ==
{{{
service zenoss start
}}}
== Optionally install the Core ZenPacks: ==
{{{
yum -y --nogpgcheck localinstall zenoss-core-zenpacks-Version.el5.x86_64.rpm
}}}
== Getting Started ==
{{{
http://server-IP-address:8080
}}}
