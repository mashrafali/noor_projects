=== System Preparations ===

= Install Ubuntu 12.04: =

= Edit Network interface: =
{{{	
vi /etc/network/interfaces

auto eth0
iface eth0 inet static
address '' IP ADDRESS ''
netmask '' subnet mask ''
gateway '' Gateway ''
dns-nameservers ''DNS Server IP ''
/etc/init.d/networking restart
}}}

= Update server: =
{{{
	apt-get update
	apt-get upgrade
}}}
= Install Apache: =
{{{
	apt-get install apache2 php5 libapache2-mod-php5 php5-imap php5-ldap
}}}
= install MySQL =
{{{
	apt-get install mysql-server mysql-client php5-mysql
        Enter MySQl Password:
}}}
= Install PHP MyAdmin: =
{{{
	apt-get install phpmyadmin
        Choose Apache2 as web server to be reconfigure 
        Enter Password
}}}
= Install unzip =
{{{
        apt-get install unzip
}}}

=== Installing Ticketing system ===
 
== download osticket from www.osticket.com ==
{{{
	cd /var/www
    	wget http://www.osticket.com/dl/osticket_1.6.0.tar.gz
}}}
== Unzipp package: ==
	root@ticketing:/var/www# tar xzvf osticket_1.6.0.tar.gz	
	
== Copy upload content ==
{{{
        cd /var/www/osticket_1.6.0/upload
        root@ticketing:/var/www/osticket_1.6.0/upload# cp -R * /var/www
}}}
 
YOU CAN REMOVE RAR Package to free space

== Create DB: ==
navigate to include folder
{{{
	 cd include
	 root@ticketing:/var/www/include#
         ***create a copy of ost-config.sample.php and rename to ost-config.php***
	 cp ost-config.sample.php ost-config.php

         ***Change permission on the configuration file***
         chmod for file
	 chmod 777 ost-config.php
}}}

== Create Database: ==

Procedure for setting up a MySQL user account
{{{

	mysql -u root -p                    *** Log in as root ***   
	mysql> CREATE DATABASE osticket;    *** Create a new mysql database called osticket***
	mysql> GRANT ALL ON osticket.* TO osticket@localhost IDENTIFIED BY 'Server PASSWORD'; ***Create a new user called osticket for database osticket***

}}}

=== Osticket Installation: ===
      
        open http://{SERVERIP}/setup
	follow instructions inputting details created before for database


== Change permission of ost-config.php to remove write access ==
{{{
        chmod 644 include/ost-config.php
        Delete setup directory
        After verifying that your installation completed correctly please delete setup folder.
	navigate to webroot
	cd /var/www
	remove setup directory
	rm -r setup
}}}

== Admin Panel: ==

  http://{SERVERIP}/scp/admin.php
  We need to configure Post installation settings:
	E-mail 
	Departments
	Groups
	Staff members
== Enable - Helpdesk Status Online (Active) Offline (Disabled) ==     
