{{{
Author       : Ayman Tohamy
Title        : Enable cPanel MySQL "Improved" extension
Version      : 1.1
Creation date: 2015-10-13
Latest update: 2015-10-13
}}}
<<TableOfContents(4)>>

== Check hosts file ==
Check Local hosts and resolve files and it must like below example

{{{
cpanel [~]# cat /etc/hosts

127.0.0.1               localhost localhost.localdomain localhost4 localhost4.localdomain4
::1                     localhost localhost.localdomain localhost6 localhost6.localdomain6
172.17.13.23            cpanel.noorservices.org cpanel
}}}

{{{
# cat /etc/resolv.conf
nameserver 217.139.0.38
nameserver 8.8.8.8
nameserver 208.67.220.220
}}}

If you change this file restart network service
{{{
# service network restart
}}}

== Install the MySQLi extension via cPanel WHM ==
To install the MySQLi extension via cPanel WHM, check out the following steps:

     1. Log into your '''cPanel WHM''' (Root Panel, not Domain Panel)
 1. Find the '''EasyApache (Apache Update) '''link, in the Software section
 1. On the EasyApache page, ensure '''Previously Saved Config''' is selected and click the '''Start customizing based on profile '''button''''''
 1. Keep clicking '''Next''', until you reach the '''Short Options List''' page and scroll to the bottom of the page
 1. Click on the '''Exhaustive Options List''' button
 1. On this page, scroll down to the '''PHP''' section and find '''MySQL "Improved" extension'''
 1. Ensure the check box is filled in and scroll to the bottom
 1. Click the '''Save only''' button
 1. On the next page, click the '''Build profile I just saved''' button
 1. A pop box will appear and ask you to recompile Apache and PHP, select '''Yes '''and''' I understand'''
 1. Wait until the  Build ouput is complete and the MySQLi extension should now be installed.

== Check installed Mysqli ==
To check mysqli was installed:


Run the following command as 'root' in SSH:
{{{
cpanel [~]# php -i | grep -i mysqli
}}}

== Apache Backup Profile ==
Backup Profile
{{{
# ll /usr/local/apache.backup
}}}
