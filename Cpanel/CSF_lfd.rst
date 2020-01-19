{{{
Author       : Karim Farrag & Ayman Tohamy
Version      : 1.1
Creation date: 2014-12-01
Latest update: 2015-10-12
}}}
<<TableOfContents(3)>>

Config Server Firewall (or CSF) : <<BR>>

is a free and advanced firewall for most Linux distributions and Linux based VPS. In addition to the basic functionality of a firewall – filtering packets – CSF includes other security features, such as login/intrusion/flood detections. CSF includes UI integration for cPanel, DirectAdmin and Webmin, but this tutorial only covers the command line usage. CSF is able to recognize many attacks, such as port scans, SYN floods, and login brute force attacks on many services. It is configured to temporarily block clients who are detected to be attacking the cloud server.<<BR>>

== Installation ==
1- To install CSF in cPanel


Installation
============
Installation is quite straightforward:

{{{
cd /usr/src
rm -fv csf.tgz
wget https://download.configserver.com/csf.tgz
tar -xzf csf.tgz
cd csf
sh install.sh

Next, test whether you have the required iptables modules:

perl /usr/local/csf/bin/csftest.pl
}}}
== Configurations ==
2- configuration:<<BR>> By default, the firewall is set to testing mode and many of the features are turned off. To turn your firewall on and modify the settings edit up your csf.conf file located in:<<BR>> <<BR>>

{{{
cd /etc/csf
nano csf.conf
}}}
<<BR>> Be sure to chmod the conf file to 600 once you have downloaded it and change the following line:<<BR>>

{{{
TESTING = "1"
to
TESTING = "0"
}}}
<<BR>> At this point you are done. Your firewall is configured, your logins are being monitored, and you have basic DDoS protection. Read on if you are ready for more advanced configurations.<<BR>> <<BR>> Change the connection tracking limit. Connection tracking checks how many connections a visitor is opening to your server. It's effective in blocking small attacks. set the CSF to block any IP using more than 200 connections per second. You may make the value higher or lower, but if you set it too low it will block legitimate visitors, and if you set it too high it won't catch small DOS attacks.<<BR>>  <<BR>>

{{{
CT_LIMIT = "200"
}}}
<<BR>> Turn DShield, Spamhaus, or Bognos IP blocking on or off. <<BR>> Spamhaus run a tight ship and only block IP addresses that are known spammers beyond any doubt. Set the value to 0 to disable, 1 to enable.<<BR>> <<BR>>

{{{
LF_DSHIELD = "0"
LF_SPAMHAUS = "1"
LF_BOGON = "0"
}}}
<<BR>> Temporarily ban offenders rather than permanently banning them. This is a recommended change. Change 1 to a value in seconds. I recommend 3600, or an hour ban. <<BR>> <<BR>>

{{{
LF_TRIGGER_PERM = "3600"
}}}
<<BR>> IPv4 Port Settings<<BR>> <<BR>> Lists of ports in the following comma separated lists can be added using a colon (e.g. 30000:35000). <<BR>> <<BR>> Allow incoming TCP ports connection For Example<<BR>>

{{{
TCP_IN= 20,21,25,26,80,110,143,443,465,587,993,995,2077,2078,2082,2083,2095,2096
}}}
<<BR>> <<BR>> Removing Ports 22/2087/161 from the allow list will desiable the SSH ability unless you enabled this port for a certain IP address as shown below Go to the following file:

{{{
/etc/csf/csf.allow

And add:
# tcp|in|d=22|s=IP address or Network range
tcp|in|d=161|s=217.139.224.113
tcp|in|d=22|s=217.139.224.0/24
tcp|in|d=2087|s=217.139.224.0/24
}}}
== DDOS Mitigation ==

=== Preventing DDOS attack from csf firewall. ===
First make sure DDOS attack is not from open recursive DNS settings. To check and fix that issue please read this article – [[https://anandarajpandey.com/2014/02/10/preventing-ddos-aplification-open-resolver-attack/|Preventing DDOS aplification open resolver attack]]

This article is to set CSF firewall so that any DDOS intentional attack to your server can be prevented.

'''Step 1: open and edit CSF config file. '''

{{{
vi /etc/csf/csf.conf
}}}

=== Settings: ===

Enable connection tracking.

 CT_LIMIT is max number of connection allowed from one IP, you can set this value as per your server requirement.

{{{
CT_LIMIT =300
}}}
Set connection tracking interval.

{{{
CT_INTERVAL =30
}}}
If you want to get possible ddos attack email then enable it.

{{{
CT_EMAIL_ALERT =1
}}}

If you want to make IP blocks permanent then set this to 1, otherwise blocks
will be temporary and will be cleared after CT_BLOCK_TIME seconds

{{{
CT_PERMANENT = 1
}}}
If you opt for temporary IP blocks for CT, then the following is the interval
in seconds that the IP will remained blocked for (e.g. 1800 = 30 mins)

{{{
CT_BLOCK_TIME = 1800
}}}

If you only want to count specific ports (e.g. 80,443) then add the ports
to the following as a comma separated list. E.g. “80,443”

{{{
CT_PORTS = 80,23,443
}}}
'''These settings will be enough for DDOS attacks but if you are  getting more attacks even you have above option configured then we can  set few more options.'''

'''Step 2: Enable distributed attacks'''

{{{
LF_DISTATTACK = 1  
}}}
Set the following to the minimum number of unique IP addresses that trigger
LF_DISTATTACK

{{{
LF_DISTATTACK_UNIQ = 2
}}}

'''Step 3: Enable distributed FTP attacks'''

{{{
LF_DISTFTP = 1
}}}
Set the following to the minimum number of unique IP addresses that trigger
LF_DISTFTP. LF_DISTFTP_UNIQ must be <= LF_DISTFTP for this to work

{{{
LF_DISTFTP_PERM =1
}}}
If this option is set to 1 the blocks will be permanent
If this option is > 1, the blocks will be temporary for the specified number
of seconds

{{{
LF_DISTFTP_UNIQ = 3
}}}

'''Step 4: Enable distributed SMTP attacks.'''

{{{
LF_DISTSMTP =1
}}}
Set the following to the minimum number of unique IP addresses that trigger
LF_DISTSMTP. LF_DISTSMTP_UNIQ must be <= LF_DISTSMTP for this to work

{{{
LF_DISTSMTP_UNIQ =4
}}}
If this option is set to 1 the blocks will be permanent
If this option is > 1, the blocks will be temporary for the specified number
of seconds

{{{
LF_DISTSMTP_PERM =1
}}}
This is the interval during which a distributed FTP or SMTP attack is
measured

{{{
LF_DIST_INTERVAL = 300
}}}

More Options:
{{{
Denial of Service and Flood Attacks
Several settings can help mitigate and block DOS attacks against your server.

SYNFLOOD turns on protection against SYNFLOOD attacks, while SYNFLOOD_RATE and SYNFLOOD_BURST adjusts the amount of blocking that occurs. This should only be used while you are under attack, as it will slow down legitimate traffic to your server.

UDPFLOOD, UDPFLOOD_LIMIT, and UDPFLOOD_BURST is the same as above, except it prevents outbound packets from being sent. This usually occurs when your server has been compromised and a malicious script has been uploaded. It essentially helps prevent your server from being used to attack others.

CONNLIMIT limits the number of concurrent connections to specific ports. You input values by listing the port, then the maximum number of connections. Put a ; in between each number. So if you wanted to limit port 20 to 10 connections and port 25 to 100 connections, you would enter 20;10;25;100.

PORTFLOOD limits the number of connections that can be made to a specific port over time from a single IP address. For example, if you wanted to limit TCP connections on port 20 to 10 per ever 120 seconds, you would enter 20;tcp;10;120. The block would be removed 120 seconds after the final packet from the final connection is sent. You add additional ports by separating them with commas, for instance 20;tcp;10;120,21;upd;5;120.

ICPM_IN and ICPM_IN_LIMIT restrict pings on your server. ICPM_IN can be set to 0 to disallow them completely, while ICPM_IN_LIMIT can be set to the maximum number of pings from one IP address per second. You can generally leave the limit at default, 1/s, but you may increase it if you need. You may have ping timeouts if you ping your server on the default setting, so increasing it or turning it off (by setting it to 0) may be preferred.
}}}
== Enable User Interface UI ==

Edit File: /etc/csf/csf.conf

{{{
UI = "1"
UI_PORT = "6666"
UI_IP = ""
#UI_USER = "username"
UI_USER = "noor"
#UI_PASS = "password"
UI_PASS = "complexpassword”
Restrict access only from our premises in (/etc/csf/ui/ui.allow)
Add rule for TCP/IN in csf.allow (/etc/csf/csf.allow)
tcp|in|d=6666|s=217.139.224.0/24
}}}

** Don't forget to set the strong login, after-all this is your server firewall!!!

to access it you can use your SSL hostname or IP, example 
{{{
https://41.187.100.24:6666
https://cpanel.noorhosting.com:6666
}}}



== Backup and Restore ==

From GUI :
{{{
 ConfigServer Security & Firewall - Firewall Profiles - Backup --> 

Creating backup...

Creating backup...
`/etc/csf/csf.conf' -> `/var/lib/csf/backup/1464595111_backup
}}}

Manual Backup CSF (Source server)

{{{
# cd /etc/csf
# tar cvfz csfbackup.tgz csf.conf csf.allow csf.deny csf.*ignore
}}}
Install CSF (Destination server):

----------
{{{
Installation
============
Installation is quite straightforward:

cd /usr/src
rm -fv csf.tgz
wget https://download.configserver.com/csf.tgz
tar -xzf csf.tgz
cd csf
sh install.sh

Next, test whether you have the required iptables modules:

perl /usr/local/csf/bin/csftest.pl
}}}

Restore CSF (Destination server):

----------
{{{
# cd /etc/csf
# scp -r user@source_server_IP:/etc/csf/csfbackup.tgz .
# cp -r /etc/csf /etc/csf.Backup
#  tar -xzf csfbackup.tgz
# csf -r
}}}
== Uninstall ==
to uninstall CSF

{{{
cd /etc/csf
sh uninstall.sh
}}}
