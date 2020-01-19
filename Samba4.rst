## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Samaba4
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
}}}

5- Download Samab4 source 
{{{
$ git clone http://gitweb.samba.org/samba.git samba-master
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
root@samba4:~/samba-master# /usr/local/samba/bin/samba-tool domain provision
No nameserver found in /etc/resolv.conf
Realm: Exapmle.com
 Domain [Exapmle]: 
 Server Role (dc, member, standalone) [dc]:   
 DNS backend (SAMBA_INTERNAL, BIND9_FLATFILE, BIND9_DLZ, NONE) [SAMBA_INTERNAL]: 
 DNS forwarder IP address (write 'none' to disable forwarding) [none]: 217.139.0.38

}}}


to start Samba as a Domain controller
{{{
/usr/local/samba/sbin/samba
}}}


to show all the available options for the Samba-tools write the following command:
{{{
root@samba:~# /usr/local/samba/bin/samba-tool -help
}}}
the result will be"
{{{
Available subcommands:
  dbcheck     - Check local AD database for errors.
  delegation  - Delegation management.
  dns         - Domain Name Service (DNS) management.
  domain      - Domain management.
  drs         - Directory Replication Services (DRS) management.
  dsacl       - DS ACLs manipulation.
  fsmo        - Flexible Single Master Operations (FSMO) roles management.
  gpo         - Group Policy Object (GPO) management.
  group       - Group management.
  ldapcmp     - Compare two ldap databases.
  ntacl       - NT ACLs manipulation.
  processes   - List processes (to aid debugging on systems without setproctitle).
  rodc        - Read-Only Domain Controller (RODC) management.
  sites       - Sites management.
  spn         - Service Principal Name (SPN) management.
  testparm    - Syntax check the configuration file.
  time        - Retrieve the time on a server.
  user        - User management.
  vampire     - Join and synchronise a remote AD domain to the local server.
}}}

To list Samba users:
{{{
/usr/local/samba/bin/samba-tool user -help 

Available subcommands:
  add          - Create a new user.
  create       - Create a new user.
  delete       - Delete a user.
  disable      - Disable an user.
  enable       - Enable an user.
  list         - List all users.
  password     - Change password for a user account (the one provided in authentication).
  setexpiry    - Set the expiration of a user account.
  setpassword  - Set or reset the password of a user account.
}}}
