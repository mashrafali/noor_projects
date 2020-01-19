{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-03-20
Latest update: 2014-03-20
}}}
<<TableOfContents(3)>>

This howto outlines a few extra steps that can be taken to further secure a SSH server

== Install Open SSH server ==
{{{
#Ubuntu
apt-get install openssh-server
/etc/init.d/ssh restart

#Centos
yum install openssh-server
/etc/init.d/sshd restart
or
service sshd start
}}}
== Change SSH settings ==
To completely secure SSH , you can do below steps:

 1. Choose a strong password
 1. Install “DenyHosts” to auto-block bad clients
 1. Change the default port
 1. Disable insecure Protocol 1; allowing only Protocol 2
 1. Disable root login
 1. Reduce MaxStartups
 1. Reduce LoginGraceTime
 1. Allow only specific users or groups to connect
 1. Allow only specific IP addresses to connect
 1. Allow only users with keys to connect; no passwords allowed
 1. Bind the ssh server to a specific network interface

== Edit hosts files ==
=== Edit hosts.allow file ===
__Warning : Don't close your ssh session before you test login with another ssh session after changes  __

{{{
# vi /etc/hosts.allow
}}}
{{{
#Ayman Tohamy
# for Ubuntu
sshd: 192.168.0.0/16 217.139.224.0/24

# for centos or ubuntu
sshd: 192.168.0.0/255.255.0.0
sshd: 172.17.0.0/255.255.0.0
sshd: 217.139.224.0/255.255.255.0
}}}
=== Edit hosts.deny file ===
Then in /etc/hosts.deny, configure the sshd service to deny connections from all other hosts:

{{{
# vi /etc/hosts.deny
}}}
{{{
sshd: ALL
}}}
=== Restart SSH service ===
{{{
#Ubuntu
/etc/init.d/ssh restart

#Centos
/etc/init.d/sshd restart
}}}
=== Test ===
Open new session for testing and don't close current one.



=== Change SSH port & protocol ===
For example we will change SSH port & protocol to be 2 instead of protocol 1

{{{
vi /etc/ssh/sshd_config
}}}
{{{
#Port 22
Port 1980
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::
# activation of protocol 1
Protocol 2
}}}
=== Allow new Port at Firewall ===
Ensure that there is firewall rule allow this new port 1980 Example:- if you have Ubuntu firewall installed run below command

{{{
ufw allow 1980
}}}
=== Test new SSH port ===
Test with new session to establish SSH connection using new port 1980 (don't close SSH current one)
