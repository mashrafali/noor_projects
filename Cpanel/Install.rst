{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-09-30
Latest update: 2013-09-30
}}}
<<TableOfContents(3)>>

= cPanel requirements =

https://documentation.cpanel.net/display/ALD/Installation+Guide+-+System+Requirements

== Firewall ==



{{{

For Centos 7

Run the following commands to deactivate the firewall, where ~/firewall.rules represents the firewall rules file:

# iptables-save > ~/firewall.rules
# systemctl stop firewalld.service
# systemctl disable firewalld.service
}}}

== Diable Utilities ==

{{{
For Centos 7

Run the following commands to disable NetworkManager:

# systemctl stop NetworkManager.service
# systemctl disable NetworkManager.service
# systemctl enable network.service
# systemctl start network.service
# systemctl start ipaliases.service
}}}

== Disable SELINUX ==

To disable SELinux security features, use one of the following methods:

1- Use the graphical interface to disable SELinux while you configure your operating system, and then reboot the server.

2- Edit the /etc/selinux/config file to set the SELINUX parameter to disabled, and then reboot the server.

 The contents of the /etc/selinux/config file should resemble the following example...

{{{

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
# enforcing - SELinux security policy is enforced.
# permissive - SELinux prints warnings instead of enforcing.
# disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of these two values:
# targeted - Only targeted network daemons are protected.
# strict - Full SELinux protection.
SELINUXTYPE=targeted

}}}

Important:
{{{
Make certain that the number sign ( # ) does not precede SELINUX=disabled. If a # precedes this configuration option, the system ignores the option.
}}}

= cPanel Installation =

== Manually cPanel Installation (recommended) ==
1- Install Centos


2- check hostname

{{{
    hostname mail.elsewedyind.com
}}}
4- Install cPanelpackage

Before you transfer the accounts, install cPanel & WHM on your new server. To do so, follow the steps in our [[https://documentation.cpanel.net/display/ALD/Installation+Guide|Installation Guide]].

{{{
To install cPanel & WHM on your server, run the following command:
}}}

{{{
# cd /home && curl -o latest -L https://securedownloads.cpanel.net/latest && sh latest
}}}
{{{
This command changes your session to the home directory, downloads the latest version of cPanel & WHM, and runs the installation script.
}}}
During the installation process, select a hostname and main IP address for the new server.


{{{
 # tail -f /var/log/cpanel-install*
 ----------------------------------------------------------------------------------------------------
    2013-09-22 13:47:43  144 (DEBUG):     => Log opened from cPanel Update (upcp) - Slave at Sun Sep 22 13:47:43 2013
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]   Running Standardized hooks
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]         cPanel update completed
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]   A log of this update is available at /var/cpanel/updatelogs/update.1379849850.log
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]   Removing upcp pidfile
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743] Completed all updates
    2013-09-22 13:47:43  144 (DEBUG):     => Log closed Sun Sep 22 13:47:43 2013
    2013-09-22 13:47:43  144 (DEBUG):   - ssystem [END]
    2013-09-22 13:47:43  149 ( INFO): Adding services to chkconfig.
    2013-09-22 13:47:43  151 ( INFO):  - Enabling cpanel
    2013-09-22 13:47:43  152 (DEBUG):   - ssystem [BEGIN]: /sbin/chkconfig --level 35 cpanel on
    2013-09-22 13:47:43  152 (DEBUG):   - ssystem [END]
    2013-09-22 13:47:43  151 ( INFO):  - Enabling mysql
    2013-09-22 13:47:43  152 (DEBUG):   - ssystem [BEGIN]: /sbin/chkconfig --level 35 mysql on
    2013-09-22 13:47:44  152 (DEBUG):   - ssystem [END]
    2013-09-22 13:47:44  151 ( INFO):  - Enabling sshd
    2013-09-22 13:47:44  152 (DEBUG):   - ssystem [BEGIN]: /sbin/chkconfig --level 35 sshd on
    2013-09-22 13:47:44  152 (DEBUG):   - ssystem [END]
    2013-09-22 13:47:44  488 ( INFO): Enabling cphulkd ...
    2013-09-22 13:47:44  495 ( INFO): Done
    2013-09-22 13:47:44  167 ( INFO): cPanel install finished in 79 minutes and 53 seconds!
}}}

== Automated Installation (not Recomended) ==
1- Download http://httpupdate.cpanel.net/cPanel-CentOS-6.4-x86_64.iso

2- create VM and reboot from it

3- Choose Time Zone [Africa - Cairo]

4- Enable Eth0 --> Manualy

{{{
       For Example:
         IP : 192.168.0.44/24
         G.W: 192.168.0.1
         DNS: 192.168.0.4
}}}
{{{
 Disable IPV6
}}}
Finish - '''ONCE Restart Centos will update and Cpanel will install automatically. '''

To monitor the installation process please run:

{{{
tail -f /var/log/cpanel-install*
}}}
{{{
   2013-09-22 13:47:42  144 (DEBUG):     => Log opened from cPanel Update (upcp) - Slave at Sun Sep 22 13:47:42 2013
    2013-09-22 13:47:42  144 (DEBUG):     [20130922.134742]   Post-sync cleanup completed successfully
    2013-09-22 13:47:42  144 (DEBUG):     [20130922.134742]   Polling updatenow to see if a newer version is available for upgrade
    2013-09-22 13:47:42  144 (DEBUG):     => Log closed Sun Sep 22 13:47:42 2013
    2013-09-22 13:47:42  144 (DEBUG):     ----------------------------------------------------------------------------------------------------
    2013-09-22 13:47:42  144 (DEBUG):     => Log opened from /usr/local/cpanel/scripts/updatenow at Sun Sep 22 13:47:42 2013
    2013-09-22 13:47:42  144 (DEBUG):     [20130922.134742]   Detected version '11.38.2.7' from version file.
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]   Using mirror '208.43.108.66' for host 'httpupdate.cpanel.net'.
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]   Target version set to '11.38.2.7'
    2013-09-22 13:47:43  144 (DEBUG):     [20130922.134743]   Up to date (11.38.2.7)
    2013-09-22 13:47:43  144 (DEBUG):     => Log closed Sun Sep 22 13:47:43 2013
    2013-09-22 13:47:43  144 (DEBUG):
}}}

= cPanel Configurations =
__'''1- Access and configure services:  '''__

For Test purpose:

 . - Add local host file [C:\Windows\System32\drivers\etc\hosts]

{{{
192.168.0.44                localhost.localdomain
192.168.0.44                mail.elsewedyind.com
}}}
 . http://192.168.0.44/whm
 . https://192.168.0.44:2087/
  * '''License:''' Accecpt  (Trial Lisence if you are testing and you can upgrade it later)
  * '''E-mail address''' : (i.e. ayman.tohamy@noor.net )
  * '''Hostname''':  mail.elsewedyind.com
  * '''Resolver''' :  add Resolver name servers --> for example  [ 8.8.8.8  &  217.139.0.38 ]
  * '''Network device''' --> Keep default ( Eth0   or Eth1)
  * '''IP address''' --> As we will use one IP address, Skip adding new one (use it if you publish diffrenet accounts to different IPs)
  * '''DNS configurations '''--> Use default BIND
  . Add '''Name servers''':    dns1.noor.net     &   dns0.noor.net
  * '''Mail''' : Use default Mail services.
  * '''cPHulk''' : [ enable : Send notification when brute force user is detected]
  * '''Quota''' : Use default Quota size [250 MB]
  * '''New Features''' : Enable all new features

__'''2- License: '''__

 . Run below command using root:

{{{
        /usr/local/cpanel/cpkeyclt
}}}
__'''3 - Create new account'''__ (i.e. elsewedy) with domain name elsewedyind.com or restore existent Backup.

= Related Links: =
Cpanel Backup : http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/Backup

Cpanel Backup to NFS http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/Backup_NFS

Cpanel Restore : http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/Restore
