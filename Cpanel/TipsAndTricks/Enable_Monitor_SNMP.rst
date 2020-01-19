{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Enable and Monitor cPanel SNMP service.
Creation date: 2016-12-05
Latest update: 2016-12-05
}}}
 . <<TableOfContents(4)>>

1- Installing SNMP and some optional SNMP utilities is as simple as running one command:

{{{
	# yum -y install net-snmp net-snmp-utils

}}}

2- start the SNMP service to reload the new configuration file:

{{{
	# service snmpd restart
}}}

3- Configure SNMP to start when the server boots:

{{{
	# chkconfig snmpd on
}}}

4- Adding SNMP to cPanel chkservd service

{{{

  	# echo "service[snmpd]=x,x,x,/etc/init.d/snmpd restart,snmpd,root" > /etc/chkserv.d/snmpd
	# echo "snmpd:1" >> /etc/chkserv.d/chkservd.conf

	# /scripts/restartsrv_tailwatchd
}}}

5- Enable Monitor of SNMP
{{{
  	- WHM - Service manager - Additional Services - SNMP --> Monitor --> Check.

}}}
