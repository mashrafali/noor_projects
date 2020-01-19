{{{
Author       : Ayman Tohamy
Title        : How to Move All cPanel Accounts from One Server to Another
Version      : 1.1
Creation date: 2015-10-21
Latest update: 2015-10-22
}}}
<<TableOfContents(4)>>

== Introduction ==
This  tutorial explains how to migrate your cPanel accounts, SSL  certificates, and main server IP address from one server to another. Typically, you would do this when you need to replace your server.

Note:

This  procedure assumes that your server's IP addresses are portable, and  that you can migrate them from the old server to the new server. If you  use a third-party data center or hosting provider for your cPanel  server, contact them to confirm that you can migrate your IP addresses.

Warning:

Your websites will likely experience downtime during this process.

== Migrate your cPanel accounts ==
----
{{https://documentation.cpanel.net/download/attachments/2719796/1.jpg?version=6&modificationDate=1444858800563&api=v2|https://documentation.cpanel.net/download/attachments/2719796/1.jpg?version=6&modificationDate=1444858800563&api=v2|class="confluence-embedded-image"}}

=== Install cPanel & WHM on your new server ===
Before you transfer the accounts, install cPanel & WHM on your new server. To do so, follow the steps in our [[https://documentation.cpanel.net/display/ALD/Installation+Guide|Installation Guide]].

Please refer to WIKI page How to install cPanel and apply all cPanel pre-installation requirements:

http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/Install 

During the installation process, select a hostname and main IP address for the new server.

 . {{{
Example:

-       New Server Temp IP Address: 41.187.100.30
-       Hostname : cpanel.noorhosting.com
-       Old server IP Address: 41.187.100.24
http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/Install
}}}

Important:

 * You '''must''' select a main IP address that is different from your old server.
  . e.g. 41.187.100.30
 * You  should, however, select the same hostname, shared IP address, and  nameservers as your old server. This is important because the system  configures the DNS zone files of restored accounts with those settings.  You can do this in WHM's ''[[https://documentation.cpanel.net/pages/viewpage.action?pageId=1507826|Basic cPanel & WHM Setup]]'' interface (''Home >> Server Configuration >> Basic cPanel & WHM Setup'').
 {{{
-       http://41.187.100.30/whm
-       https://41.187.100.30:2087/
-       Server Contact Email Address : cpanel@noor.net
-       Hostname : cpanel.noorhosting.com
-       Primary DNS : 217.139.0.38
-       Main Network : eth0
-       Name Server Configuration : Disabled
-       Name Servers : ns1.noorhosting.com  ns2.noorhosting.com
-       FTP : Pure-FTPD
-       Mail : Dovecot
-       cPHulk : Enable all check boxes.
-       Quotas: Use file system quotas.
-       Feature Showcase: Enable ALL or customize them.
}}}

Notes:

 * cPanel,  Inc. automatically provides you with free 15-day trial license when you  install cPanel & WHM on a new server. However, if the [[http://cpanel.net/cpanel-whm/trial-license/|Trial License Interface]] shows that your IP address is '''not''' eligible for a trial license, [[http://cpanel.com/company/#contact|contact Customer Service]].
 * The migration process will transfer your permanent license to the new server.

----
{{https://documentation.cpanel.net/download/attachments/2719796/2.jpg?version=6&modificationDate=1444858800672&api=v2|https://documentation.cpanel.net/download/attachments/2719796/2.jpg?version=6&modificationDate=1444858800672&api=v2|class="confluence-embedded-image"}}

=== Copy all accounts to the new server ===
Important:

If you do '''not'''  use a SNI-capable operating system, be sure to copy all SSL  certificates before you switch servers. SSL certificates and keys exist  in the /etc/ssl/ directory.

After you  install cPanel & WHM on your new server, copy each account from your  old server to your new server. To move all of your accounts from one  server to another, follow the steps listed in WHM's ''[[https://documentation.cpanel.net/display/1144Docs/Transfer+Tool|Transfer Tool]] ''interface (''Home >> Transfers >> Transfer Tool'').

 . {{{
Example:
Transfer Tool:-
-       Remote Server : 41.187.100.24
-       Remote SSH Port : 22
-       Login : root
-       Password:
-       Advanced : Remote server Type (cPanel & WHM) , Threads (4)
-       Fetch Account List
-       Select All packages and ALL Accounts then press “COPY”
}}}

----
{{https://documentation.cpanel.net/download/attachments/2719796/3.jpg?version=6&modificationDate=1444858800758&api=v2|https://documentation.cpanel.net/download/attachments/2719796/3.jpg?version=6&modificationDate=1444858800758&api=v2|class="confluence-embedded-image"}}

=== Shut down your old server ===
Important:

Before you begin Step 3, if your old server is a member of a DNS cluster, delete the DNS clusters in WHM's ''[[https://documentation.cpanel.net/display/ALD/DNS+Cluster|DNS Cluster]]'' interface ''(Home >> Clusters >> DNS Cluster)''.

After you copy your accounts to the new server, you can shut down your old server.

----
{{https://documentation.cpanel.net/download/attachments/2719796/4.jpg?version=6&modificationDate=1444858800846&api=v2|https://documentation.cpanel.net/download/attachments/2719796/4.jpg?version=6&modificationDate=1444858800846&api=v2|class="confluence-embedded-image"}}

=== Change the new server’s main IP address to the old server’s IP address ===
After  you shut down your old server, change the new server’s main IP  address to the IP address of your old server. Perform this action via  the command line in a local session. A local session allows you to make  changes, restart the network service, and stay connected to the server.

To change the server's main IP address, perform the following steps:

 1. Open the /etc/sysconfig/network-scripts/ifcfg-eth0 file with a text editor.
 {{{
# vi /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE="eth0"
BOOTPROTO="static"
DNS1="217.139.0.38"
GATEWAY="41.187.100.1"
HWADDR="00:50:56:AC:CD:BC"
IPADDR="41.187.100.24"
IPV6INIT="no"
NM_CONTROLLED="yes"
ONBOOT="yes"
PREFIX="27"
TYPE="Ethernet"
UUID="3a369f0a-fb8b-4c71-95db-d3313b8325b2"
}}}

 1. Edit the IPADDR and GATEWAY lines to use the IP address and gateway of your old server.
 1. Open the /etc/ips file with a text editor.
 1. Add your old server's primary IP address, net mask, and gateway to the file. Note:

Remove the new server’s primary IP address from this file if it is present.

 1. Restart the network service with the following commands:
  * For CentOS, CloudLinux™, and Red Hat® Enterprise Linux (RHEL) 6 and earlier, and Amazon Linux, run the service network restart command. Note:

Amazon Linux '''always''' runs in a NAT configuration.

 * For CentOS, CloudLinux, and RHEL 7 and later, run the systemctl restart network command.

 * Run the /scripts/mainipcheck command to add the IP address to the /var/cpanel/mainip file.
 {{{
/scripts/mainipcheck
}}}
 * Run the /scripts/fixetchosts command to add the IP address and hostname of your server to the /etc/hosts file.
 {{{
/scripts/fixetchosts
}}}

Access WHM and if not working due to license please run below command via SSH

{{{
# /usr/local/cpanel/cpkeyclt
}}}
----
{{https://documentation.cpanel.net/download/attachments/2719796/5.jpg?version=6&modificationDate=1444858800942&api=v2|https://documentation.cpanel.net/download/attachments/2719796/5.jpg?version=6&modificationDate=1444858800942&api=v2|class="confluence-embedded-image"}}

=== Change the IP address of all accounts to the correct IP address ===
After  you transfer the accounts to the new server, the system may have set  the IP addresses of the accounts to the new server’s original main IP  address. You '''must''' change each account's IP address to the current IP address with WHM's ''[[https://documentation.cpanel.net/display/ALD/IP+Migration+Wizard|IP Migration Wizard]]'' interface (''Home >> IP Functions >> IP Migration Wizard'').

{{{
New IP : 41.187.100.24
Old IP : 41.187.100.30
}}}
----
{{https://documentation.cpanel.net/download/attachments/2719796/6.jpg?version=6&modificationDate=1444858801044&api=v2|https://documentation.cpanel.net/download/attachments/2719796/6.jpg?version=6&modificationDate=1444858801044&api=v2|class="confluence-embedded-image"}}

=== Join the new server to the DNS cluster ===
Note:

If your server is '''not''' a member of a DNS Cluster, skip ahead to Step 7.

To add the new server to the DNS cluster, perform the following steps:

 1. Navigate to WHM's'' [[https://documentation.cpanel.net/display/ALD/DNS+Cluster|DNS Cluster]]'' interface (''Home >> Clusters >> DNS Cluster'').
 1. Select ''Enable DNS Clustering''.
 1. Click ''Change''.
 1. Click ''Return to Cluster Status''.
 1. Select the server to add to the DNS cluster from the ''Add a new server to the cluster'' menu.
 1. Click ''Configure''.

----
{{https://documentation.cpanel.net/download/attachments/2719796/7.jpg?version=6&modificationDate=1444858801150&api=v2|https://documentation.cpanel.net/download/attachments/2719796/7.jpg?version=6&modificationDate=1444858801150&api=v2|class="confluence-embedded-image"}}

=== Reinstall all SSL certificates ===
Note:

Skip ahead to Step 8 if your server meets the following conditions:

 * The accounts on your server do '''not''' use SSL certificates from a Certificate Authority.
 * Both of your servers use an SNI-capable operating system.

To upload an existing SSL certificate to your server, perform the following steps:

 1. Navigate to WHM's ''[[https://documentation.cpanel.net/display/ALD/Install+an+SSL+Certificate+on+a+Domain|Install an SSL Certificate on a Domain]]'' interface ''(Home >> SSL/TLS >> Install an SSL Certificate on the Domain).''
 1. Paste the certificate into the text box, or click ''Fetch ''to upload a *.crt file.
 1. Click ''Submit'' to install the certificate onto the server.

----
{{https://documentation.cpanel.net/download/attachments/2719796/8.jpg?version=6&modificationDate=1444858801232&api=v2|https://documentation.cpanel.net/download/attachments/2719796/8.jpg?version=6&modificationDate=1444858801232&api=v2|class="confluence-embedded-image"}}

=== Confirm your cPanel license is valid ===
After you move all of your accounts to your new server, confirm that your cPanel license is valid. To do so, go to our [[http://verify.cpanel.net/|License Verification interface.]]

=== TIPs and Tricks ===
 . 1-      cPanel Shared Hosting (DNS cluster)

 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/shared_hosting
  .
  . 2-      cPanel Backup
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/Backup_NFS
  .
  . 3-      ClamAV
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/ClamAV
  .
  . 4-      cPanel CSF (firewall)
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/CSF_lfd
  .
  . 5-      cPanel EXIM sending limit (attach – Limit per hour – recipients/message)
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/TipsAndTricks/Sending_mail_message_limit
  .
  . 6-      Change Time and PHP upload size
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/TipsAndTricks/Change_time
  .
  . 7-      Enable RBLs
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/TipsAndTricks/Enable_RBLs
  .
  . 8-      cPanel DNS Backup
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/DNS_Backup
  .
  . 9-      cPanel Mysqli
 . http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/TipsAndTricks/Mysqli
  .
  . 10-   Notifications
  . -          Home »Server Configuration »Tweak Settings- Notifications
              Enable mailbox usage warnings <<BR>>             Bandwidth limit check
  .
  . 11-      Password Policy (65 % - Age 180 days)
  . -          Home »Security Center »Password Strength Configuration à Default 65%
  . -          Home »Security Center »Configure Security Policies àEnable Password Strength & Password Age = 180 days
  .
  . 12-      SSL Redirection
  . -          Home »Server Configuration »Tweak Settings- redirection : Always redirect to SSL
