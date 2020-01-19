{{{
Author       : Ayman Tohamy
Title        : Configure cPanel with EasyApache
Version      : 1.1
Creation date: 2016-02-04
Latest update: 2016-02-04
}}}
<<TableOfContents(4)>>

== Overview ==
EasyApache is  software that installs, modifies, and validates your Apache web server,  PHP, Tomcat, and other components of your web server.

For an introduction to EasyApache and how to troubleshoot common problems, read our [[https://documentation.cpanel.net/display/EA/EasyApache+FAQ|EasyApache FAQ]] documentation.

cPanel  & WHM does not require that you use EasyApache, but it provides a  convenient and easy method to modify your web server. Your cPanel &  WHM license includes the EasyApache software.

Important:

 * We '''strongly''' recommend that you update your cPanel & WHM version before you run EasyApache.

 * We do '''not'''  support EasyApache functionality on versions of cPanel & WHM that  have reached End of Life (EOL). If your version of cPanel & WHM does  not support a new feature in EasyApache, your server will not have  access to that feature.
 * For more information on our EOL policy, read our [[https://documentation.cpanel.net/pages/viewpage.action?pageId=1507947|cPanel & WHM Long-Term Support]] documentation.

=== The EasyApache interface ===
WHM's ''EasyApache 3'' interface (''Home >> Software >> EasyApache 3'')  allows you to select different options in EasyApache. You can also  access a command line interface (CLI) version of the EasyApache  interface with the /scripts/easyapache script.

For more information on WHM's ''EasyApache 3'' interface, read our [[https://documentation.cpanel.net/display/EA/EasyApache+Interface|EasyApache Interface]] documentation.

=== EasyApache profiles ===
An  EasyApache profile contains a set of options that determine the modules  that EasyApache builds into your web server. EasyApache provides  profiles that include the most commonly used options.

The initial stage of WHM's ''EasyApache 3'' interface allows you to modify, build, or download a profile.

After you modify a profile, you can save your selections as a new profile. To save your selections as a new profile, use the ''Save selections as custom profile (optional)'' section at the bottom of the ''Exhaustive Options List'' stage of the ''EasyApache 3'' interface.

Note:

 . The /var/cpanel/easy/apache/profile/custom/ directory contains your profile files. For example, if you enter exampleprofile in the ''File name'' text box, exampleprofile.yaml  will be the full file name.

For more information on how to distributes EasyApache profiles to multiple servers, read our [[https://documentation.cpanel.net/display/EA/How+to+Distribute+EasyApache+Files+Over+Multiple+Servers|How to Distribute EasyApache Files Over Multiple Servers]] documentation.

=== How EasyApache works ===
When you access WHM's ''EasyApache 3'' interface,  EasyApache checks for any updates to its own software and the software  that it provides. EasyApache will not make changes to your server until  you initiate the build process.

The EasyApache software performs the following steps when you initiate the build process:

 1. EasyApache creates a backup copy of your current Apache configuration.
 1. EasyApache  checks the options that you selected and verifies compatibility, and  then attempts to build Apache and PHP with your selections.
 1. If the new build passes validation tests, EasyApache applies the build to your server. Warning: This step may cause your web server to be briefly unresponsive.

 1. EasyApache  checks to make sure that the new build is fully functional on your  server. If Apache does not function properly, EasyApache may revert your  Apache installation to the configuration from step 1.
 1. EasyApache removes temporary files and exits.

=== EasyApache file locations ===
The following table includes some important EasyApache file locations:

{{{
/usr/local/cpanel/logs/easy/apache/     This directory includes the build log files of your EasyApache build attempts.

/var/cpanel/easy/apache/profile/_main.yaml      This file contains your most recent EasyApache build profile.

/scripts/easyapache     This script provides access to the CLI version of the EasyApache interface.

/var/cpanel/easy/apache/runlog  This directory contains the build history files. You can also access your EasyApache build history via the Profile stage of WHM's EasyApache 3 interface.
}}}
== Check hosts file ==
Check Local hosts file and it must like below example (e.g. do not use localhost with public IP).

{{{
cpanel [~]# cat /etc/hosts

127.0.0.1               localhost localhost.localdomain localhost4 localhost4.localdomain4
::1                     localhost localhost.localdomain localhost6 localhost6.localdomain6
41.187.100.24           cpanel.noorhosting.com cpanel
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
== Update Apache to Easy Apache via cPanel WHM ==
For example to install the MySQLi extension via cPanel WHM, check out the following steps:

 1. Log into your '''cPanel WHM''' (Root Panel, not Domain Panel)

 1. Find the '''EasyApache (Apache Update) '''link, in the Software section
 1. On the EasyApache page, ensure '''Previously Saved Config''' is selected and click the '''Start customizing based on profile '''button
 1. Keep clicking '''Next''', until you reach the '''Short Options List''' page and scroll to the bottom of the page
 1. Click on the '''Exhaustive Options List''' button
 1. On this page, scroll down to the '''PHP''' section and find '''MySQL "Improved" extension'''
 1. Ensure the check box is filled in and scroll to the bottom
 1. Click the '''Save only''' button
 1. On the next page, click the '''Build profile I just saved''' button
 1. A pop box will appear and ask you to recompile Apache and PHP, select '''Yes '''and''' I understand'''
 1. Wait until the  Build ouput is complete and the MySQLi extension should now be installed.

== Upgrade Apache to EasyApache via CLI ==
If you run a cPanel server, and need to upgrade your Apache or PHP  version, cPanel provides the Easyapache tool to make these updates a  breeze. While it can be run from WHM, it is generally preferred to run  it from the command line.

 . '''Note: '''before you get started, make sure that you are running the below commands in a ( screen). If your ssh connection drops, screen will keep the easyapache process running on the server for you.

{{{
cpanel [~]# screen
Directory '/var/run/screen' must have mode 777.
cpanel [~]#
cpanel [~]#
@cpanel [~]# chmod 777 /var/run/screen
cpanel [~]# screen
}}}
=== Starting Easyapache ===
As root, on the command line, in screen, run:

{{{
/scripts/easyapache
}}}
This will bring up an interactive menu. Unless you completely want to  rebuild your apache and php configuration from the ground up, leave the  selection on '''Previously Saved Config''', then use the Tab key and the arrow keys until you have selected '''Start customizing based on profile'''.

{{attachment:easyapache-cli-01.png}}


Hit '''Enter''' to move to the next screen. Here you can  choose which version of Apache you wish to run on your server. While  Easyapache allows you to install versions of Apache that have reached  end of life status, it is ''not'' recommended.

{{attachment:easyapache-cli-02.png}}

=== Upgrading PHP ===
When you choose the version of PHP you wish to change to, make sure  to research your site’s code base to see if it works with the new  version. Keep in mind that Easyapache installs Zend Optimizer for PHP  5.2, and Zend Guard for PHP 5.3 and above; again, check to see that your  site code is compatible before making changes.

{{attachment:easyapache-cli-03.png}}

On the next screen, '''Short Options List''', you can  select the most commonly used Apache and PHP modules. If there is a  module you wish to install that is not on this list, select '''Exhaustive Options List'''. Otherwise, click '''Save and Build''' to begin the compile process.

{{attachment:easyapache-cli-04.png}}

=== Starting the Compile ===
Once you have hit '''Save and build''', it is time to sit  back and let Easyapache do its thing. At the end of the process, it will  briefly restart apache. Until that time, do not restart apache, even if  it appears to be down temporarily. Should you do so, you will not be  able to bring apache back up until Easyapache can complete a new  compile, leaving your sites down the whole time.

=== After the Compile ===
When the Easyapache process has finished, take some time to test your  sites and make sure that they still run fine with the new versions of  Apache and PHP. If you run into any trouble, you can run a new  Easyapache using a saved configuration.

== Apache Backup-Restore Profile ==
Backup Profile

{{{
# ll /usr/local/apache.backup
}}}

Easy Apache Logs:
{{{
/usr/local/cpanel/logs/easy/apache/

# tail -f /usr/local/cpanel/logs/easy/apache/build.1454511516
}}}

Backing Up an EasyApache Profile:

The easyapache profile file is saved in:
{{{
/var/cpanel/easy/apache/profile/_main.yaml
}}}

 
Restoring an EasyApache Profile:

The custom profiles (those that you see as choices in the initial easyapache screen) live in the directory:

{{{
/var/cpanel/easy/apache/profile/custom/
}}}

== Easy Apache Localhost & HTTP Error ==

If you receive below error after running easy apache upgrade

{{{
-- Begin test 'Pre-test htaccess check' --
!! Test passed ok !!
-- End test 'Pre-test htaccess check' --
-- Begin test 'Basic HTTP Request for static content' --
localhost did not have any working mirrors. Please check your internet connection or dns server. at /usr/local/cpanel/Cpanel/HttpRequest.pm line 986.
}}}

Simply ensure that easy apache process is ended then remove EasyApache build file

{{{
root@as [~]# cd /usr/local/apache


root@as [/usr/local/apache]# rm AN_EASYAPACHE_BUILD_IS_CURRENTLY_RUNNING
rm: remove regular empty file `AN_EASYAPACHE_BUILD_IS_CURRENTLY_RUNNING'? y
root@as [/usr/local/apache]#
}}}
