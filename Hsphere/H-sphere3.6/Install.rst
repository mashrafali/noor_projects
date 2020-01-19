## page was renamed from Enterprise/IT/Docs/HowTo/Linux/H-sphere/H-sphere3.6/Install
{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-11-07
Latest update: 2013-11-07
}}}
<<TableOfContents(7)>>

= Preparing for Parallels H-Sphere Installation =
This chapter provides comprehensive information on  how to prepare Linux and Unix servers for the installation of Parallels  H-Sphere components.

'''Note:''' configuration  files for Apache, FTP, PHP, DNS, MySQL, and PostgreSQL should be  customized indirectly via respective template files. Otherwise, all  custom changes in major default configuration files are removed with  each H-Sphere update! If you need those files customized, please  carefully follow the [[http://hsphere.parallels.com/docs/3.6.1/installation/cp/html/conf_file_template_customization.html|configuration file templates customization instruction in Appendix C]].

In this chapter:

== Supported Operating Systems ==
||<tablewidth="480px" tablestyle="border-collapse:collapse"width="240px" style="border:1px solid #010101;                                          ">Operating System ||<width="240px" style="border:1px solid #010101;                                          ">Supported OS Version ||
||<width="240px" style="border:1px solid #010101;                                          ;text-align:center" |4>'''!RedHat Enterprise Linux''' ||<width="240px" style="border:1px solid #010101;                                          ;text-align:center" |3>5.x (i386, x86-64) ||
||<width="42px"> ||
||<width="42px"> ||
||<width="240px" style="border:1px solid #010101;                                          ">6.x (i386, x86-64) ||
||<width="240px" style="border:1px solid #010101;                                          ;text-align:center" |3>'''CentOS''' ||<width="240px" style="border:1px solid #010101;                                          ;text-align:center" |2>5.x (i386, x86-64) ||
||<width="42px"> ||
||<width="240px" style="border:1px solid #010101;                                          ">6.x (i386, x86-64) ||
||<width="240px" style="border:1px solid #010101;                                          ">'''!CloudLinux''' ||<width="240px" style="border:1px solid #010101;                                          ">5.6 (i386, x86-64) 6.3 (i386, x86-64) ||
||<width="240px" style="border:1px solid #010101;                                          ;text-align:center" |2>'''FreeBSD''' ||<width="240px" style="border:1px solid #010101;                                          ">8.3 (i386, x86-64) (experimental) ||
||<width="240px" style="border:1px solid #010101;                                          ">7.4 (i386, x86-64) ||


'''Important:''' 1. FreeBSD: Control Panel installed on a server with 64-bit operating system requires glibc 32-bit compatibility library. 2.  CloudLinux is supported as a RedHat Enterprise Linux clone. The  customizations allowed by it are not propagated to UI, and should be  performed manually according to instructions published on the CloudLinux  site, if needed.

=== Install Centos 5.5 ===
{{{
        - Install Centos 5.5 " You should have 7 CDs"
            - Choose Time Zone (Africa - cairo)
            - Manullaly IPv4 Example: 192.168.0.105/24     Gateway:192.168.0.1
              Disable IPV6
            - Choose Server not Desktop
            - You will prompt for CD1 to CD6 [From Vmware point to them]
            - Reboot.

            - You will find Tool     --> FireWall         -->  Security and Selinux --> Selinux:disabled
                --> Software Services         --> Remove Iptables and add NFS, rsync
            - restart Server

            - Login SSH
            - Update
                # yum update
                # yum install update
}}}
[[http://hsphere.parallels.com/docs/3.6.1/installation/cp/html/7985.htm|Single-Server and Multi-Server Installation]]

== Single-Server and Multi-Server Installation ==
=== General Considerations ===
Parallels H-Sphere can be installed on one or more  servers. The required number of servers and their hardware configuration  will largely depend on the number of accounts you are planning to host,  Web and mail quotas, traffic load and other related factors.

Here are some general considerations common to Parallels H-Sphere server environment:

 * We recommend installing  Control Panel to a separate server. It is also acceptable to install one  DNS server to the Control Panel server box, for example, if you are  planning 2-server installation.
 * You '''must not'''  install PostgreSQL hosting service on the same box with Control Panel,  as the latter requires a separate PostgreSQL server for its system  database.
 * You can have several DNS servers on  one box. However, for multiserver Parallels H-Sphere installation, you  should install each DNS server on a separate box. The best solution is  to have two DNS servers on separate boxes.
 * We advise installing mail server on the same box with MySQL server, as mail server requires its own MySQL database.
 * It  is reasonable to allocate separate physical servers for the most  resource-consuming services. Usually, these are Web and mail servers,  but sometimes it may be MySQL and PostgreSQL.

According to these recommendations, the following '''4-server installation''' may be an optimal solution:

 * '''Server 1:''' Control Panel (with the system PostgreSQL database);
 * '''Server 2:''' Web1 + DNS1;
 * '''Server 3:''' Mail + MySQL1 (user DB) + DNS2;
 * '''Server 4:''' PostgreSQL (user DB) + MySQL2 (user DB).

Later on, you may add more boxes to your system, as your needs grow:

 * '''Server 5:''' Web2;
 * '''Server 6:''' Mail2 (with its own MySQL DB);

=== Hardware Requirements ===
If you are going to install Parallels H-Sphere to  only one computer, make sure it is at least Pentium III, 500MHz CPU and  512MB RAM. This will allow to host only a small number of customers.  Adding Parallels SiteStudio will require ''at least'' 1000MHz CPU and 1GB RAM.

=== HDD Partitioning ===
Parallels H-Sphere is installed to the /hsphere directory.

We recommend dedicating a separate partition for the Parallels H-Sphere installation directory and mount it as /hsphere.

{{{
# mkdir -p /hsphere
# chmod 755 /hsphere
}}}
Parallels H-Sphere directory can be located on any  other partition as well. However, we do not recommend installing  Parallels H-Sphere to the root / partition. Having Parallels H-Sphere on  the root partition may cause certain problems. For instance, if disk  quota gets damaged, you cannot repair it without server reboot and fsck check in the single user mode.

If your Parallels H-Sphere installation directory is to be located on another partition, for example, /usr/hsphere on the /usr partition, the /hsphere symlink to this directory must be anyway created:

{{{
# mkdir -p /usr/hsphere
# ln -s /usr/hsphere /hsphere
# chmod 755 /usr/hsphere
}}}
== Required Components and Configuration ==
=== SELinux Must Be Off ===
''(RedHat Enterprise Linux 4, CentOS 4 and up, and White Box Enterprise Linux 4 only)''

Before Parallels H-Sphere installation, ''make sure SELinux is off ''on your Linux servers.

To check SELinux status, run:

{{{
selinuxenabled && echo $?
}}}
If as a result of this command you receive 0, SELinux is enabled. No result means that SELinux is off.

To disable SELinux, set the following option in /etc/selinux/config:

{{{
SELINUX=disabled
}}}
This will turn off SELinux after reboot. To disable SELinux immediately, type:

{{{
setenforce 0
}}}
=== iptable ===
to access CP website URL, stop iptable:

{{{
#/etc/init.d/iptables stop
}}}
=== ifconfig ===
Make sure the ifconfig utility on the Control Panel box has '755' permissions by running:

{{{
#which ifconfig
/path/to/ifconfig
#chmod 755 /path/to/ifconfig
#chmod 755 /sbin/ifconfig
}}}
= Installing Parallels H-Sphere =
To install Parallels H-Sphere from scratch, please  carefully follow the instructions provided below in this chapter. If you  want to install Parallels H-Sphere by importing a specially formatted config.xml file, refer to [[http://hsphere.parallels.com/docs/3.6.1/installation/cp/html/10232.htm|Appendix A. Installing with prepared Parallels H-Sphere configuration]].

In this chapter:

Step 1. Running the Parallels H-Sphere Installation Script

Step 2. Installing the Parallels H-Sphere via Control Panel

Step 3. Completing Parallels H-Sphere Installation Wizard

!Installing Parallels SiteStudio with Parallels H-Sphere

== Step 1. Running the Parallels H-Sphere Installation Script ==
 1. Log into the server where you install Parallels H-Sphere as root:
 {{{
$ su -l
}}}
 1. Download the Parallels H-Sphere install script: Linux:
 {{{
# wget http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0P1/U36.0P1
}}}
 FreeBSD:
 {{{
# fetch http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0P1/U36.0P1
}}}
 1. Run the install script with the install option:
 {{{
# sh ./U36.0P1 install
}}}
 1. Choose the cpinstall  mode to install basic Parallels H-Sphere packages on the Control Panel  server to be able to run Parallels H-Sphere Control Panel. In the  installer's command line type in the following command:
 {{{
cpinstall zone=SERVICE_ZONE ip=SERVICE_IP

Ex:
cpinstall zone=noorgroup.com ip=192.168.0.105
}}}
 where SERVICE_ZONE is the service domain name (for example, '''noorgroup.com'''), and SERVICE_IP is the service domain IP.

This will start the Control Panel installation process.

== Step 2. Installing the Parallels H-Sphere via Control Panel ==
 1. Upon successful installation, run Parallels H-Sphere Control Panel at http://'''SERVICE_IP''':8080/ in your favorite browser. '''Note: '''If you have problems with running Control Panel, please try to .
 1. Log into Parallels H-Sphere Control Panel as''' admin '''with password '''admin'''. Later on you will be able to change the administrator password. After you log in, you must enter your valid Parallels H-Sphere license purchased from the ''Buy Parallels Support and Services for Parallels H-Sphere ''page.  Otherwise, you won't be able to proceed with the installation. H-Sphere  3.2 and up introduces licensing through a unified Parallels licensing  system.
 1. Trial License :
 {{{
        http://www.parallels.com/psoft/contact/trial/
        A00M00-ZN1702-KDR158-TYSS19-95QX97      --> atohamy@noor.net
        A00X00-KEW702-88VX53-BJMZ24-TT5K80
}}}
 {{attachment:1.jpg}}
 1. Run Parallels H-Sphere Installation  Wizard which allows shaping your Parallels H-Sphere cluster by adding  boxes and hosting services and configuring basic Parallels H-Sphere  settings.<<BR>>
 1. __'''Change CP URL with hostname not IP address'''__<<BR>><<BR>>In this case, you need to change your hostname to your CP domain name instead of the IP address:<<BR>>1.Log into your CP server as the cpanel user:<<BR>>
 {{{
    su -l cpanel
}}}
 2.Edit the hsphere.properties
 {{{
    cp ~cpanel/shiva/psoft_config/hsphere.properties ~cpanel/shiva/psoft_config/hsphere.properties.backup
}}}
 {{{
    vi ~cpanel/shiva/psoft_config/hsphere.properties
}}}
  . In the CP_HOST field, enter the domain name instead of the IP address.<<BR>>
 {{{
# CP_HOST = 192.168.0.105
 CP_HOST = cp.noorgroup.com
}}}
 <<BR>>3.Restart H-Sphere.<<BR>>login with root<<BR>><<BR>>For Linux:
 {{{
     /etc/rc.d/init.d/httpdcp restart
}}}

=== Running Parallels H-Sphere Installation Wizard ===
Now that you ran the Parallels H-Sphere updater in the installation mode with the cpinstall option, you need to:

==== Step 1. Manage configuration file ====
{{attachment:2.jpg}}

Installation Wizard writes the Parallels H-Sphere cluster configuration into the specially formatted config.xml file (see [[http://download.hsphere.parallels.com/HSdocumentation/xmls/config.xml|sample config.xml]]). The '''Configuration File''' section on the main page enables you to:

 * '''Import: '''You  upload the prepared XML file from a local machine to Parallels H-Sphere  and later reconfigure Parallels H-Sphere in the wizard. Read more on  Parallels H-Sphere installation from prepared config.xml file;
 * '''Export:''' export config.xml with your Parallels H-Sphere cluster configuration to your local machine.
 * '''Restore to Default:''' choose this option to recreate config.xml and to restart configuring Parallels H-Sphere cluster in the wizard.

==== Step 2. Edit general settings ====
 1. Click the '''Edit General Settings''' icon on the right corner of the '''General Settings''' caption and proceed to the following page: {{attachment:4.jpg}}
 1. * '''System Domain:''' Specify the service domain name here.
  * '''One Server Installation''': check this box if you need a single server installation. You can't add more than one physical server by checking this options.
  * '''Use NAT IP mapping:''' Check this box if you implement [[http://hsphere.parallels.com/docs/3.6.1/installation/cp/html/nat.html|NAT]] on your Parallels H-Sphere.
  * {{attachment:6.jpg}}
 1. Press '''Submit''' and return to the main page of the wizard.

==== Step 3. Add physical servers ====
 1. Click the '''Add Physical Server''' icon on the right corner of the '''Physical Servers''' caption. {{attachment:7.jpg}}
 1. Fill in the form for adding new physical servers and services:
 {{attachment:12.jpg}}
 1. Set physical server Name, IP, root  password, and choose which logical servers (Control Panel, Web, mail,  DNS, MySQL, PostgreSQL) will be installed there. They will be installed  with default settings which can be changed if necessary.<<BR>> {{attachment:13.jpg}} <<BR>>
 1. Click '''Submit'''. After you have added physical servers into Parallels H-Sphere cluster, you will see them on the main page of the wizard:

{{attachment:17.jpg}}

 . We recomended Export Configurations after finish :config.xml
 . {{{
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<hsinst>
    <physicalServers>
        <physicalServer password="password" name="cp.noorgroup.com" id="22">
            <logicalServers>
                <logicalServer name="cp.noorgroup.com" id="22" group="cp">
                    <ips>
                        <ip type="service">
                            <addr>192.168.0.105</addr>
                            <mask>255.255.255.0</mask>
                        </ip>
                    </ips>
                </logicalServer>
                <logicalServer name="ns1.noorgroup.com" id="23" group="dns">
                    <ips>
                        <ip type="service">
                            <addr>192.168.0.105</addr>
                            <mask>255.255.255.0</mask>
                        </ip>
                    </ips>
                    <options>
                        <option value="master" name="named_role"/>
                    </options>
                </logicalServer>
            </logicalServers>
            <ip type="service">
                <addr>192.168.0.105</addr>
                <mask>255.255.255.0</mask>
            </ip>
        </physicalServer>
        <physicalServer password="password" name="web.noorgroup.com" id="23">
            <logicalServers>
                <logicalServer name="web.noorgroup.com" id="24" group="unix_hosting">
                    <ips>
                        <ip type="shared">
                            <addr>192.168.0.106</addr>
                            <mask>255.255.255.0</mask>
                        </ip>
                    </ips>
                </logicalServer>
                <logicalServer name="pgsql.noorgroup.com" id="25" group="pgsql">
                    <ips>
                        <ip type="service">
                            <addr>192.168.0.106</addr>
                            <mask>255.255.255.0</mask>
                        </ip>
                    </ips>
                </logicalServer>
                <logicalServer name="mysql.noorgroup.com" id="26" group="mysql">
                    <ips>
                        <ip type="service">
                            <addr>192.168.0.106</addr>
                            <mask>255.255.255.0</mask>
                        </ip>
                    </ips>
                </logicalServer>
            </logicalServers>
            <ip type="service">
                <addr>192.168.0.106</addr>
                <mask>255.255.255.0</mask>
            </ip>
        </physicalServer>
        <physicalServer password="password" name="mail.noorgroup.com" id="24">
            <logicalServers>
                <logicalServer name="ns2.noorgroup.com" id="27" group="dns">
                    <ips>
                        <ip type="service">
                            <addr>192.168.0.108</addr>
                            <mask>255.255.255.0</mask>
                        </ip>
                    </ips>
                    <options>
                        <option value="slave1" name="named_role"/>
                    </options>
                </logicalServer>
                <logicalServer name="mail.noorgroup.com" id="28" group="mail">
                    <ips>
                        <ip type="service">
                            <addr>192.168.0.108</addr>
                            <mask>255.255.255.0</mask>
                        </ip>
                    </ips>
                </logicalServer>
            </logicalServers>
            <ip type="service">
                <addr>192.168.0.108</addr>
                <mask>255.255.255.0</mask>
            </ip>
        </physicalServer>
    </physicalServers>
    <systemzone>noorgroup.com</systemzone>
</hsinst>
}}}
 . Here you may also configure physical server profiles, setting update rules for each physical server or groups of servers. Click '''Manage install/update profiles''' below the Physical Servers form to configure the profiles: {{http://hsphere.parallels.com/docs/3.6.1/installation/cp/html/9156.png||align="bottom",height="211",width="709"}}

==== Step 4. Add logical servers ====
Once physical server is added, you can add logical servers:

 1. Select the type of logical server you want to add and click '''Submit'''. Choosing '''Use defaults for this server''' will apply default names for Parallels H-Sphere logical servers on this server. By default, they are named web'''N''', mail'''N''', ns'''N''', mail'''N''', mysql'''N''', respectively.least
 1. For example :  we need at least two DNS servers, we can add Logical DNS to new phusical mail server" <<BR>><<BR>> {{attachment:14.jpg}}

You can edit logical server parameters, if necessary.

 * {{attachment:15.jpg}} {{attachment:16.jpg}}
 * {{{
    You can edit logical server parameters, if necessary.
    --> DNS : ns1.noorgroup.com
    --> DNS Role : Master (Primary DNS)
        22     cp.noorgroup.com     192.168.0.105        255.255.255.0     cp
        23     ns1.noorgroup.com 192.168.0.105        255.255.255.0     dns (Master DNS Role)

    --> Choose : Return to Main Pre-configuration Page

Add another servers:
    - unix physical web server + Mysql + Pgsql : web.noorgroup.com
        24     web.noorgroup.com     192.168.0.106    255.255.255.0 unix_hosting
        25     pgsql.noorgroup.com     192.168.0.106    255.255.255.0 pgsql
        26     mysql.noorgroup.com     192.168.0.106    255.255.255.0 mysql

    - unix mail server + NS : mail.noorgroup.com & ns2.noorgroup.com
        27     ns2.noorgroup.com     192.168.0.108    255.255.255.0 dns (Slave 1 DNS Role)
        28     mail.noorgroup.com     192.168.0.108    255.255.255.0 mail
}}}

==== Step 5. Choose between full and reduced installation ====
Now that you're done with Parallels H-Sphere configuration:

 1. Click '''Proceed With Installation Wizard.''' '''Note: '''If you choose to '''Skip Installation Wizard''', you will be taken directly to Administrator Control Panel and all your pre-configuration will be lost. {{attachment:18.jpg}}
 1. Choose the installation mode: '''full '''or '''reduced''' installation. {{attachment:19.jpg}} <<BR>><<BR>> {{attachment:20.jpg}}
 1. Press Start to start update then proceed.
 {{{
    ---> Choose with Proceed With Installation Wizard.
    ---> Choose update
    --> If have error update with server whom configured as DNS [ns1.noorgroup.com & ns2.noorgroup.com]
        /hsphere/pkg/updates/U36.0/U36.0P1/update_07.11.13_05_36.log

     -->Edit /etc/hosts
        199.115.104.141 download.hsphere.parallels.com

    --> Test resolving and ping download.hsphere.parallels.com
    --> Update Again
}}}

Aborted Installation If at any point of your installation process, you:

 * Go to your wizard '''Home''' in the middle of installation
 * Loose connection with your browser
 * Skip installation (as on Step 5)

you will have a choice of further actions:

 * '''Proceed with Installation Wizard''' will take you to the point of interrupted installation process
 * '''Go to Admin CP''' will take you to your administrator panel without completing installation
 * '''Clear Installed Data''' will cancel all settings and you can start installation from scratch

== Step 3. Completing Parallels H-Sphere Installation Wizard ==
There are two alternative ways to complete full installation: ''via Control Panel web interface'' or ''running Parallels H-Sphere install/update script with postinstall option''.

=== Option One: completing installation via Control Panel web interface ===
 1. On the page that appears check the servers you want to be updated/installed and click '''Start'''. To see the update log, click the server name link. On  multiserver installations, you can see the update process  simultaneously on each server. To do it, click the logical server links  to switch between the server logs. Update process indicator {{http://hsphere.parallels.com/docs/3.6.1/installation/cp/html/9161.png||align="bottom",height="12",width="23"}} legend:
  * '''Yellow:''' ready for update
  * '''Blue:''' update is running
  * '''Green:''' update successfully finished
  * '''Red:''' update finished with error. If update fails, you will see an error message with details.<<BR>>'''Important:'''  You don't need to restart the whole update process if the update fails  only on a certain server. Just log into this server manually, fix  possible issues and resume this step on this particular server.
  * {{attachment:22.jpg}}
 1. When update is finished and the light turns green, click '''Proceed''' to complete installation.
 1. On the page that appears, click '''Return to Admin CP'''.<<BR>><<BR>> {{attachment:23.jpg}}
 1. You will be taken to administrator Control Panel.

=== Option Two: Completing installation with postinstall option ===
Return to the [[#Step_1._Running_the_Parallels_H-Sphere_Installation_Script]] and choose the postinstall option there:

{{{
postinstall
}}}
= Installing H-Sphere WinBox =
== Preparing Windows Server ==
This chapter gives you information on how to prepare  Windows 2003, Windows 2008 x64, and Windows R2 x64 servers for  installation of Parallels H-Sphere components and notes you should  consider before proceeding to the installation.

'''Notes: '''

{{{
- Parallels  H-Sphere Winbox 3.4 and later are not compatible with Parallels H-Sphere  3.3 and earlier. Update your Parallels H-Sphere to the respective 3.4+  version first.
- We don't install Parallels H-Sphere on live servers.
- On Windows, the users group by default has the read/write permissions  on all disks. Upon Parallels H-Sphere installation, the users group is  stripped of the read/write permissions on the system directory and other  directories that may affect hosting. No other permissions are modified.
If needed, change them by yourself.
}}}
=== Hardware and Partitioning Requirements ===
For better functioning we recommend having not less than 512 MB RAM. And as for partitions it's better to break it into two:

 * C:\ for system files and program files - at least 5 GB.
 * D:\ for users' content - the rest of the HDD.

'''Important:'''''' '''The''' '''partition containing users' content must be NTFS-formatted to support disk space quotas.

=== Software Requirements ===
Before installing Parallels H-Sphere to Windows  platform, make sure your system complies with the following software  requirements:

 1. 64-bit only version of  Windows 2008 Server and Windows 2008 R2 or 32-bit only version of  Windows 2003 Server with the following limitations:
  * Server  must not be PDC (Primary Domain Controller), i.e Parallels H-Sphere  cannot be installed on Windows 2003/2008 SBS because it works only as  PDC.
  * Server must not be in Active Directory mode.
 1. Windows must be up-to-date, all recommended updates must be installed.
 1. IIS6/IIS7 (Internet Information Server) must be set up for WWW and FTP services.<<BR>><<BR>>
 {{{
--> For Windows server 2008 R2 >> Server Manager --> Roles --> Add Web server IIS --> Enable All features
}}}
 On  Windows 2008, ASP.NET 1.1 must be installed (for installation  instructions on Windows 2008 refer to the article at
  . http://blogs.iis.net/bills/archive/2008/06/02/installing-asp-net-1-1-with-iis7-on-vista-and-windows-2008.aspx).
 {{{
Install Framework v1.1, SP1, and ASP.NET's security update to SP1:
--------------------------------------------------------------------------------------
 - Disable IE Enhancement Security [Server manager - Configure IE ESC -- Turn Off]
    •.NET Framework Version 1.1 Redistributable Package
        http://www.microsoft.com/en-us/download/details.aspx?id=26
        [ Direct X - Net Framework4]

    •.NET Framework Version 1.1 Service Pack 1
        http://www.microsoft.com/en-us/download/details.aspx?id=33

    •ASP.NET Security Update for .NET Framework 1.1 SP1
        http://www.microsoft.com/en-us/download/details.aspx?id=2551

        Enable ASP.NET v1.1 ISAPI as an allowed ISAPI extension.
            To do this, open "IIS Manager" administration tool.
             In the features view, click on the "ISAPI and CGI Restrictions" feature.--> Allow

         If doesn't exist --> click "add"
            Extension: C:\Windows\Microsoft.NET\Framework\v1.1.4322\aspnet_isapi.dll
            note: change drive if your system drive is not C:\
            Description: ASP.NET v1.1
}}}

(Optional)  ASP.NET 4.0 (for installation instructions refer to the article at   http://msdn.microsoft.com/en-us/library/5a4x27ek.aspx).

{{{
    http://msdn.microsoft.com/en-us/library/5a4x27ek.aspx
    http://www.microsoft.com/en-us/download/details.aspx?id=40779
}}}
 1. The following ports must be open:
  * 20, 21 (FTP)
  * 80 (HTTP)
  * 443 (HTTPS)
  * 873 (Rsync)
  * 10125 (SOAP) Parallels  H-Sphere includes support for Parallels H-Sphere Windows Services. When  creating a new account on winbox, Windows service method is run with  SOAP (Simple Object Access Protocol). It's a protocol of data  communication between CP and Windows server. To enable support for Win  services in Parallels H-Sphere, go to hsphere.properties file and set: SOAP_SUPPORT = true Also, make port 10125 available on Windows  server. This port is used to connect to Windows services by default. It  can be set in the hsphere.properties file: SOAP_PORT = 10125<<BR>><<BR>>
  {{{
[root@cp ~]# vi ~cpanel/shiva/psoft_config/hsphere.properties
[root@cp ~]# vi /hsphere/local/home/cpanel/shiva/psoft_config/hsphere.properties
                SOAP_SUPPORT = true
                SOAP_PORT = 10125
}}}
 1. These are the default ports for *SQL servers to be open if your customers connect to them via ODBC:
  * 3306 (MySQL)
  * 5432 (PostgreSQL)
  * 1433 (MS SQL)
  '''Note:''' If *SQL server is put on any port other than the default, don't forget to open it.

To access Windows boxes via pcAnywhere or Terminal Service, the ports 5631 and 3389 correspondingly must be open.

 1. On Windows 2003, MDAC 2.8 or later must be installed.
 1. On Windows 2003 R2, the "File Server Resource Manager" component must be installed.
 1. On Windows 2008, the following fix for IIS7 must be installed for WebShell to work: http://support.microsoft.com/kb/960267.

The following third-party commercial products are purchased separately and must be installed prior to Parallels H-Sphere:

 * MS SQL server
 * ColdFusion
 * Miva<<BR>><<BR>>__'''To install MS SQL (2005 for example)'''__
 * {{{
        --> Install SQL (SQl 2005)
            - install SQl 2005 and SQL 2005 SP3 then update and restart server.
            - Install SQL native and SQL Server 2005
            - Select all components
              (SQl Server - Notification - Analaysis - Workstation- Integrated services)
            - keep Default instance Name
            - Use Built-in System account (Local system)
            - Start all services at end of setup
            - Mixed Authentication (Windows & SQL Authentication Mode)
            - during installation will ask you about Visual studio accept and Run Program
            - Install SQL-2005 SP1 with the same settings
            - Install SQLManagementStudio to mange server:
               http://www.microsoft.com/en-us/download/details.aspx?id=8961            - Restart Server
}}}

=== ASP.NET 4.0 Installation ===
We recommend installing ASP.NET 4.0 before the  H-Sphere. However, it is also possible to install ASP.NET 4.0 later. In  this case, the following steps are needed to make it work properly:

 1. In machine.config files residing in %windir%\Microsoft.NET\Framework\v4.0.30319\Config and %windir%\Microsoft.NET\Framework64\v4.0.30319\Config the following changes should be made:
  * Attribute allowDefinition="MachineOnly" should be added to the <section name="identity"/> tag.
  * <identity impersonate="true" /> tag should be added to the <system.web> section.
 1. Ensure that ASP.NET 4.0 ISAPI modules are added to the IIS and are allowed.<<BR>>
 {{{
 To do this, open "IIS Manager" administration tool.
 In the features view, click on the "ISAPI and CGI Restrictions" feature.--> Allow ASP.NET V4
}}}

=== Peculiarities of Parallels H-Sphere's Work Under Windows 2008 x64 and Windows 2008 R2 x64 ===
Please consider the following when you intend to install Parallels H-Sphere on Windows 2008 x64 and Windows 2008 R2 x64 servers:

 1. The following third-party products are not supported:
  * SharePoint
  * OsCommerce
  * EasyAppSvc
  * ColdFusion
 1. Miva doesn't support Windows 2008 and hence is not available.
 1. SSL certificate is not correctly set when switching from shared to dedicated IP. Workaround: manually switch SSL off and on from CP.
 1. 64-bit application pools don't work properly if FTP Service is stopped. <<BR>>
 {{{
Workaround: start Windows FTP Service
[ FTP Publishing service (win2008) - Microsoft FTP Service (windows 2008 R2)].
}}}
 1. Signup UI allows entering password that doesn't meet default Windows 2008 password policy.
 {{{
Workaround: disable password complexity requirements policy:

go to Local Security Policy -> Accounts Policies -> Password Policy and set Password must meet complexity requirements to "Disabled".
}}}

=== Peculiarities of Parallels H-Sphere work under Windows 2003 ===
Please consider the following when you intend to install Parallels H-Sphere on Windows 2003 servers:

 1. Parallels H-Sphere works with IIS 6 compatible mode.
 1. In Parallels H-Sphere shared SSL scheme works with IIS 6.0.
 1. CDONTS  object library doesn't work on Windows 2003. We recommend using CDO  instead of CDONTS on all Windows servers, since it is a newer and more  flexible.

== Installing Parallels H-Sphere Winbox ==
To install Parallels H-Sphere Winbox, please perform the steps provided below in this chapter.

In this chapter:

=== Step 1. Adding Physical Server ===
'''To add a Windows physical server:'''

 1. In the Control Panel, go to '''E. Manager''' > '''Servers''' > '''Add P.Server''' and fill in the form:
  1. Enter the name of the physical server, its IP and associated net mask.
  1. Specify the name of administrative account (usually, ''hsadmin'') and its password, so that CP is able to connect to the Windows box.
  {{{
    --> Name: winweb.noorgroup.com
    --> IP1: 192.168.0.107
    --> Mask:255.255.255.0
    --> Create local account called it : admin and password : same as control panel admin
    --> login : admin
    --> Password:
    --> OS Type: Windows
}}}
  {{attachment:27.jpg}}
  1. Submit the form
 1. Add physical server groups according to the hosting type: Windows Web hosting, MS SQL hosting, or both. To add a group:
  1. .   1. Go to '''E.Manager''' -> '''Servers''' -> '''Server Groups'''.
   1. At the bottom of the page that appears, enter the name of the server group and select its type.
   1. Click '''Add'''.
  1. .   1. Go to '''E.Manager'''-> '''Servers''' -> '''P.Servers'''.
   1. Click the name of the physical server.
   1. In the '''Add Group''' field, select a server group and click''' Add'''.
   {{{
    --> win servers
    --> MS SQL Server
}}}
   {{attachment:28.jpg}}
 1. Assign  physical server profile: your Windows server will be installed and  updated according to the rules specified in this profile:
  1. Go to '''E.Manager''' > '''Update''' >''' Physical server profiles''' {{attachment:29.jpg}} <<BR>><<BR>><<BR>>
  1. Choose the box and click the '''Add''' button {{attachment:30.jpg}}
  1. Name and configure your profile in the form that appears<<BR>>
  1. {{{
New Windows Profile
        -->  Name: Win+SQL
        -->  Enable : Update only pointed logical server groups -->  MsSQL & Windows Hosting
        -->  Enable Log level : 6
        -->  Enable Location of users home (applicable for windows_hosting group only) : C:\Hosting
        -->  Enable  Name of MSSQL server instance (applicable for MsSQL group only) : winweb
}}}
  {{attachment:31.jpg}} <<BR>>
  1. Click '''Save''' to apply. Profile will appear in the list of existing profiles.
  1. Click the '''Assign Profiles''' to '''Physical Servers''' link and apply.
  {{{
        --> choose Assign Profiles to Windows Physical Servers
        --> Check Physical server (winweb.noorgroup.com) --> choose profile (Win+SQL) --> Apply
}}}
  {{attachment:32.jpg}}

=== Step 2. Adding Logical Server ===
To add a windows web logical server:

 1. In your administrator control panel go to '''E.Manager''' > '''Servers '''> '''Add L.Server'''.
 1. On the page that appears, enter the properties of the logical server.<<BR>><<BR>>
 {{{
    Name     winweb.noorgroup.com
    Group     win servers
    Server Type     Windows 2000
    Description     Windows Web Server
}}}
 {{attachment:33.jpg}} <<BR>>
 1. Click  Submit to create a logical server. You will proceed to the page where  you can configure other parameters for this logical server:
  1. Enable user signup on this server.
  1. Add IP range available for hosting on this server.<<BR>><<BR>>
  {{{
    ---> From 192.168.0.107 to 192.168.0.107
    -->  Mask : 255.255.255.0
    -->  Type: Shared IP
    -->  Press : Submit
}}}
  1. Automatically  generate custom DNS records by clicking Generate in front  of Generate  custom DNS records for Windows logical server.<<BR>>
  {{{
    --> Custom DNS record will be created :
        winweb.noorgroup.com    86400   IN      A       192.168.0.107
}}}
  1. Configure additional options specific to Windows logical server type.<<BR>>
  {{{
--> Prohibit users from switching to dedicated IP-->  set
}}}
  {{attachment:34.jpg}} <<BR>>
 1. Add/Check DNS records for this logical server in '''E.Manager '''> '''DNS Manager '''menu. Make sure you created a custom A record for this logical server.
 {{{
winweb.noorgroup.com    86400   IN      A       192.168.0.107
}}}
 {{attachment:39.jpg|39.Jpg}}
 1. To Add another MS SQL Logical server<<BR>><<BR>>
 {{{
    Name     mssql.noorgroup.com
    Group     MS SQL Server
    Server Type     Windows 2000
    Description     MS SQL Server
    Physical Server     winweb.noorgroup.com
    Available for signup     Enable
    Status     0
    IPs     192.168.0.107 [255.255.255.0] (Service IP) null Reseller Aliases use this IP
    Generate custom DNS records for this logical server
    Custom DNS record created: mssql.noorgroup.com     86400     IN     A     192.168.0.107
}}}
 1. create a logical server:<<BR>><<BR>> {{attachment:36.jpg}} <<BR>>
 1. Add IP Address Range:<<BR>><<BR>> {{attachment:37.jpg}} <<BR>>
 1. Generate Custom DNS record / Enable SignUp<<BR>><<BR>>

{{attachment:38.jpg}} <<BR>><<BR>>

=== Step 3. Adding New Windows Server to NAT Configured Cluster ===
If you are adding new Windows server to a NAT configured Parallels H-Sphere cluster, you must add this server's IPs to the:

 . ~cpanel/shiva/psoft_config/ips-map.xml file.

=== Step 4. Installing Parallels H-Sphere MSI Package ===
To install Parallels H-Sphere for Windows:

 1. Download Parallels H-Sphere Windows HsCore and HsInstaller MSI packages for your OS:
  * Windows 2003:
   1. http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0/HsCore_x86.msi
   1. http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0/HsInstaller_x86.msi
  * Windows 2008 x64 and Windows 2008 R2 x64:
   1. http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0/HsCore_x64.msi
   1. http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0/HsInstaller_x64.msi
   1. http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0/HsGeneralHosting_IIS75_x64.msi
   1. http://download.hsphere.parallels.com/shiv/HS/releases/U36.0/U36.0/HsWeb_IIS75_x64.msi
 1. Run the HsCore installer and follow its instructions.
 {{{
Note: On this step you can choose the Parallels H-Sphere folder. By default,
it is C:\Program Files\HSphere.
After the installation you won't be able to change the Parallels H-Sphere location.
}}}
 {{attachment:40.jpg}} <<BR>>
 1. Ensure that you have create local admin account at winweb server:
 {{{
    --> Create local account called it : admin and password : same as control panel admin
    ---> Enter H-spher admin username and password
    --> username:admin
    --> password:
}}}
 1. Run the HsInstaller installer and follow its instructions.<<BR>><<BR>> {{attachment:41.jpg}} <<BR>>
 1. Start H-Spher service from WINWEB server (Services.msc) <<BR>><<BR>> {{attachment:45.jpg}} <<BR>>
 1. In administrator control panel, go to '''E.Manager -> Update -> Update Boxes'''. Check your Winbox server and click '''Start Update'''  to run Parallels H-Sphere Update Wizard to complete the installation.  This will automatically install all other required packages to  your  Winbox. <<BR>>[[attachment:42.jpg]]<<BR>>
 1. Now you will have all servers updated:

[[attachment:46.jpg]]

{{{
Important:
---------
Parallels H-Sphere Winbox resources are installed by means of respective MSI packages.
Some
 third-party Parallels H-Sphere services (like Miva or ColdFusion) won't
 be installed to the Windows server if the original software is absent
on the box. First, install the third-party software, and then run the
Update Wizard on this box again to install respective Parallels H-Sphere
 MSI packages (HSMiva, HSColdFusion, etc).
}}}
