{{{
Author       : Karim Farrag
Version      : 1.1
Creation date: 2014-10-21
Latest update: 2014-10-21
}}}
<<TableOfContents(7)>>

== Introduction ==
We wanted to provide Cpanel Shared Hosting environment as for example it integrate better with Joomla if needed.

cPanel & WHM has A flexible API, access to 100s of applications, and built-in configurations (such as EasyApache) allow hosting providers that use cPanel & WHM software to fully customize their offerings. This allows you, the hosting provider, to focus your time on your business and customers, and not worrying about managing the day-to-day server tasks.

cPanel & WHM software automates business, saves both time and money. It also provides easy-to-use features that website owners love, which means customers are Happy :) .

== Technical ==
We want to configure the Cpanel WHM to work with 2 Cpanel DNSonly as name servers first we will prepare the 3 Cpanels: 1- cpanel WHM  2- 2 DNS-only Cpanel  Second step will configure the DNS cluster

== Cpanel WHM ==
1- Install Centos

2- Then Configure The Operating System IP address via the following command:

{{{
vi /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
HWADDR=
TYPE=Ethernet
UUID=
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=none
IPADDR=
DNS1=
GATEWAY=
IPV6INIT=no
PREFIX=""
USERCTL=no
}}}
3-Download and install the following script:

{{{
root@server [~]# cd /home
root@server [~]# yum install wget
root@server [~]# wget -N http://httpupdate.cpanel.net/latest
root@server [~]# sh latest
}}}
4-Kindly Note that you can re-configure the Hostname and root password and some other options with the following command after you finish the operating system installation:

{{{
sys-unconfig
}}}
Go to the server IP http://yourserverip/whm to access the Initial Setup Wizard<<BR>>

Step1:<<BR>> =======<<BR>> Agree and continue Click I Agree/Go To Step 2 to proceed to the next step in the wizard.

<<BR>>

Step2<<BR>> =======<<BR>> The second section of the Initial Setup wizard allows you to set up your server's networking information.<<BR>>

1- In the Server Contact Email Address text box, enter the email address at which you wish to receive notifications about this server (for example, user@example.com ).<<BR>> 2- Enter your server's hostname in the This Server's Hostname text box.<<BR>> 3- Resolvers in this section enter the 2 Name servers ips.<<BR>> 4- Main Network/Ethernet Device to choose the network interface in case you have more than one.<<BR>>

step3:<<BR>> =======<<BR>> Set Up IP Addresses This section allows you to add IP addresses to your server. If you do not wish to enter additional IP addresses, click Skip This Step and Use Default Settings to proceed to Step 4

<<BR>>

Step4: <<BR>>======= <<BR>>Nameservers<<BR>> This section allows you to configure name servers.  Name servers spread your server's DNS information throughout the Internet to allow visitors to access websites on your server.<<BR>> 1- choose Disable DNS<<BR>> 2- Enter the ip addresses of you 2 nameservers <<BR>>

Click Save & Go to Step 5.<<BR>>

Step5:<<BR>> =======<<BR>> This section allows you to configure the services that you and your clients use on the server.<<BR>> FTP Configuration<<BR>> Mail Configuration<<BR>> Configure cPHulk<<BR>>

 . - in this section check on send notifications<<BR>>

Step6:<<BR>> ======<<BR>> Set Up Quotas<<BR>> This is the final stage of the Initial Setup process. This section allows you to select whether you wish to track disk usage on your server.<<BR>>

Quotas<<BR>> Select whether you wish to enable quotas on your server:<<BR>> To set up quotas, select Use file system quotas.<<BR>> To disable quotas, select Do not use file system quotas.<<BR>> Save and finish<<BR>> Click Finish Setup Wizard. WHM will save your settings, and the WHM Home interface (Home) will appear.<<BR>>

== Cpanel DNSonly ==
1- Install Centos

2- Then Configure The Operating System IP address via the following command:

{{{
vi /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
HWADDR=
TYPE=Ethernet
UUID=
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=none
IPADDR=
DNS1=
GATEWAY=
IPV6INIT=no
PREFIX=""
USERCTL=no
}}}
3- Download and install the following script:

{{{
root@server [~]# cd /home
root@server [~]# yum install wget
root@server [~]# wget -N http://httpupdate.cpanel.net/latest-dnsonly
root@server [~]# sh latest-dnsonly
}}}
4- Kindly Note that you can re-configure the Hostname and root password and some other options with the following command after you finish the operating system installation:

{{{
sys-unconfig
}}}
Go to the server IP http://yourserverip/whm to access the Initial Setup Wizard<<BR>>

Step1:<<BR>> =======<<BR>> Agree and continue Click I Agree/Go To Step 2 to proceed to the next step in the wizard.<<BR>>

Step2<<BR>> =======<<BR>> The second section of the Initial Setup wizard allows you to set up your server's networking information.<<BR>>

1- In the Server Contact Email Address text box, enter the email address at which you wish to receive notifications about this server (for example, user@example.com ).<<BR>> 2- Enter your name server's hostname in the This Server's Hostname text box.<<BR>> 3- Resolvers in this section enter the 2 Name servers ips.<<BR>> 4- Main Network/Ethernet Device to choose the network interface in case you have more than one.<<BR>>

step3:<<BR>> =======<<BR>> Set Up IP Addresses<<BR>> This section allows you to add IP addresses to your server. If you do not wish to enter additional IP addresses, click Skip This Step and Use Default Settings to proceed to Step 4<<BR>>

Step4:<<BR>> =======<<BR>> Nameservers<<BR>> This section allows you to configure name servers.  Name servers spread your server's DNS information throughout the Internet to allow visitors to access websites on your server.<<BR>> 1- choose Bind DNS<<BR>> 2- Enter the ip addresses of you 2 name servers <<BR>>




 Save and finish<<BR>> Click Finish Setup Wizard. WHM will save your settings, and the WHM Home interface (Home) will appear.<<BR>>

== DNS Cluster ==
1- On the WHM cpanel Go to DNS Clustering. <<BR>><<BR>> Modify Cluster Status under the Modify Cluster Status heading: Select Enable DNS clustering to enable DNS clusters.<<BR>><<BR>>

2- Add a new server to the cluster<<BR>><<BR>> Click Configure. You will be directed to the cPanel DNS Remote Configuration interface.<<BR>><<BR>>

3- Enter the hostname or IP address of the name server that you wish to link in the Remote cPanel & WHM DNS host text box.<<BR>><<BR>>

4- Enter the WHM user name for the name server in the Remote server user name text box.<<BR>><<BR>>

5- Enter the name-server's remote access key in the Remote server access hash text box.<<BR>><<BR>>

6- then in the DNS Role: choose synchronize changes<<BR>><<BR>>

7- press submit<<BR>><<BR>> Note: <<BR>> ====== <<BR>> You can find the server's remote access key in WHM's Setup Remote Access Key interface (Home >> Cluster/Remote Access >> Setup Remote Access Key). <<BR>>
