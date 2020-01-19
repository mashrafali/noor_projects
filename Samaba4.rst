{{{
Author       : Karim Farrag
Version      : 1.0
Creation date: 2012-12-27
Latest update: 2012-12-27
}}}

This document explains how to setup a simple Samba server as an Domain Controller on Ubuntu 12.04 server.

1- Install Ubuntu server 12.04 64-bit

2- These packages are required for a successful build of samba 4 on Ubuntu server
{{{
$ apt-get install build-essential
$ apt-get install libacl1-dev libattr1-dev
$ apt-get install libblkid-dev libgnutls-dev libreadline-dev python-dev
$ apt-get install python-dnspython gdb pkg-config libpopt-dev libldap2-dev
$ apt-get install dnsutils libbsd-dev attr krb5-user docbook-xsl
$ apt-get install dnsutils libbsd-dev
$ apt-get install attr docbook-xsl
}}}

2- Update and upgrade the system

{{{
$ apt-get update
$ apt-get upgrade
}}}

3- Install krb5-user
{{{
$ apt-get install krb5-user
}}}

4- Install the git&bzr package so you can use (( git and bzr))
{{{
$ apt-get install git
$ apt-get install bzr
}}}

5- Download Samab4 source 
{{{
$ git clone git://git.samba.org/samba.git samba-master
}}}

6- Change the DNS To point to internal DNS

{{{
$ vi /etc/resolv.conf 
Nameserver 127.0.0.1
}}}

7- Go to the Samba folder:
{{{
$ cd samba-master/
}}}
Then write the following commands to Compile Samba:
{{{
$ ./configure --enable-debug --enable-selftest
$ make
$ make install
}}}

now to provision samba 
{{{
/usr/local/samba/bin/samba-tool domain provision --realm=Domain DQN --domain=domain --adminpass 'P@ssw0rd' --server-role=dc --dns-backend=BIND9_DLZ
}}}
