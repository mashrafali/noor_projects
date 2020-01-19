## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/FTP
## page was renamed from Enterprise/IT/Docs/HowTo/Linux/FTP
## page was renamed from Enterprise/IT/Docs/How_To/Linux/FTP
{{{
Author       : Haitham Hassan
Version      : 1.1
Creation date: 2012-07-11
Latest update: 2012-07-11
}}}
<<TableOfContents(3)>>

== Install the service ==
1- Install Linux Operating System then install FTP service: To install the FTP service,

{{{
yum -y install vsftpd
}}}
== Allow local user authentication and Disable anonymous access ==
2- Edit the config file to allow local user authentication and disable anonymous access

{{{
vi /etc/vsftpd/vsftpd.conf
}}}
{{{
anonymous_enable=NO
ascii_upload_enable=YES
ascii_download_enable=YES
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
ls_recurse_enable=YES
use_localtime=YES
}}}
'''__Note:__'''

{{{
1- (anonymous_enable=NO): By default the anonymous access is enabled and we should disable it .
2- (chroot_local_user=YES): FTP users are local users not from Authentication server [Radius - Active Directory].
3- (chroot_list_file=/etc/vsftpd/chroot_list): Path list for users whom have access to FTP server.
}}}
{{{
root@ftp:/home/noor# grep -v '#' /etc/vsftpd.conf

listen=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd.chroot_list
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=ftp
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
}}}
== Add user list file to access ftp server ==
3- Add user list file that permit the local users required to access ftp server

{{{
vi /etc/vsftpd/chroot_list
}}}
{{{
test
}}}
== Add user to local user Database ==
4- Add the user to local user Database

{{{
useradd -m test
passwd test
newpasswor ***********
}}}
== Modify your Iptable to allow FTP ports ==
5 -To modify your Iptable to allow FTP ports.

http://wiki.noor.net/Enterprise/IT/Docs/How_To/Linux/IP_Table

http://wiki.centos.org/HowTos/Network/IPTables

== Stop SELINUX ==
6-Stop SELinux

{{{
#setup
select option2
disable security
}}}
== Restart the FTP service ==
7- You should restart the service to activate the server

{{{
/etc/rc.d/init.d/vsftpd start
}}}
