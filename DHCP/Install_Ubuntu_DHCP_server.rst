## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Install_Ubuntu_DHCP_server


{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 2015-02-08
}}}
<<TableOfContents(3)>>

{{{

Install ubuntu 14.04 LTS 

}}}

=== Update and Upgrade packages ===

{{{

apt-get update
apt-get upgrade

}}}


=== Assign static IP for the server ===

{{{

auto eth0
iface eth0 inet static
address x.x.x.x
netmask y.y.y.y
gateway z.z.z.z
dns-nameservers i.i.i.i

}}}


=== Set up the DHCP service ===

{{{
sudo apt-get install isc-dhcp-server
}}}

=== Install Webmin ===

{{{

sudo nano /etc/apt/sources.list

And Add the following 2 lines 

deb http://download.webmin.com/download/repository sarge contrib
deb http://webmin.mirror.somersettechsolutions.co.uk/repository sarge contrib

}}}

=== Add the GPG key ===

{{{

sudo wget http://www.webmin.com/jcameron-key.asc
sudo apt-key add jcameron-key.asc

}}}

=== Update the sources list ===

{{{

sudo apt-get update

}}}

=== Install webmin using the following command ===

{{{
sudo apt-get install webmin
}}}

=== Access the webmin through web browser ===

{{{
https://ip-address:10000/
}}}
