{{{
Author       : Ahmed AbdelSattaar
Version      : 1.1
Creation date: 2014-10-27
Latest update: 2015-04-09
}}}

=== DNS Mirror ===

 * Configured as Backup server for DNS Master, DNS1 and DNS0 (Backup DNS configuration files & DNS zones).

 * Managed through control panel (the same configurations of DNS Master).
 * Configured with private interface at the Backend for security wise.
 * dnsmirror.noor.net backend --> eth0: 172.17.13.11

-  Configure new DNS server same as master server with configurations :

{{{
- IP address: 172.17.13.11
- Hostname : dnsmirror.noor.net
- Webmin same as master server. (You can Export dnsmaster.noor.net then only change its Ip address)
}}}

- Create Backup script for dns master
{{{
# vi /usr/bin/dnsmaster-backup.sh
# Create backup directory with today date
date=$(date +"%Y-%m-%d_%k:%M")

source=172.17.13.10
user=administrator
hostname=dnsmaster.noor.net
destination=/backup/$hostname/$date/

mkdir -p $destination
mkdir -p $destination/zones


# synch bind config and zone files
rsync -avz ${user}@$source:/etc/bind  $destination
rsync -avz ${user}@$source:/var/lib/bind/* $destination/zones

# stop bind and webmin services
/etc/init.d/bind9 stop
/etc/init.d/webmin stop

# synch backuped data to bind directories
rsync -avz  $destination/bind/* /etc/bind/
rsync -avz  $destination/zones/* /var/lib/bind/

# start bind and webmin services
/etc/init.d/bind9 start
/etc/init.d/webmin start
}}}

- Create Backup script for dns0
{{{
# vi /usr/bin/dns0.noor.net-backup.sh 
--------------
# Create backup directory with today date
date=$(date +"%Y-%m-%d")

source=172.17.30.118
user=administrator
hostname=dns0.noor.net
destination=/backup/$hostname/$date/

mkdir -p $destination
mkdir -p $destination/zones


# synch bind config and zone files
rsync -avz ${user}@$source:/etc/bind  $destination
rsync -avz ${user}@$source:/var/lib/bind/* $destination/zones
}}}

- Create Backup script for dns1

{{{
# vi /usr/bin/dns1.noor.net-backup.sh 
--------------
# Create backup directory with today date
date=$(date +"%Y-%m-%d")

source=172.17.13.38
user=administrator
hostname=dns1.noor.net
destination=/backup/$hostname/$date/

mkdir -p $destination
mkdir -p $destination/zones


# synch bind config and zone files
rsync -avz ${user}@$source:/etc/bind  $destination
rsync -avz ${user}@$source:/var/lib/bind/* $destination/zones
-------------
}}}
- make script executable:
{{{
# chmod +x /usr/bin/dnsmaster-backup.sh
# chmod +x /usr/bin/dns0.noor.net-backup.sh
# chmod +x /usr/bin/dns1.noor.net-backup.sh
}}}

- Create and Copy public key from dns mirror to dns master
{{{
# ssh-keygen -t rsa
# scp /root/.ssh/id_rsa.pub administrator@172.17.13.10:/tmp/
}}}

- Login to dns master then run
{{{
# cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
# chmod 700  ~/.ssh/authorized_keys
}}}

- Test scp without password
{{{
scp /root/.ssh/id_rsa.pub administrator@172.17.13.10:/tmp/
}}}


- Create and Copy public key from dns mirror to dns0
{{{
# ssh-keygen -t rsa
# scp /root/.ssh/id_rsa.pub administrator@172.17.20.17:/tmp/
}}}

- Login to dns0 then run
{{{
# cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
# chmod 700  ~/.ssh/authorized_keys
}}}

- Test scp without password
{{{
scp /root/.ssh/id_rsa.pub administrator@172.17.20.17:/tmp/
}}}



- Create and Copy public key from dns mirror to dns1
{{{
# ssh-keygen -t rsa
# scp /root/.ssh/id_rsa.pub administrator@172.17.13.18:/tmp/
}}}

- Login to dns master then run
{{{
# cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
# chmod 700  ~/.ssh/authorized_keys
}}}

- Test scp without password
{{{
scp /root/.ssh/id_rsa.pub administrator@172.17.13.18:/tmp/
}}}



- Login to dns mirror and then test Backup Script 
{{{
root@dnsmirror # dnsmaster-backup.sh
root@dnsmirror # dns0.noor.net-backup.sh 
root@dnsmirror # dns1.noor.net-backup.sh 

}}}


- Add backup script to crontab
{{{
# crontab -e
# m h  dom mon dow   command
0 7,19 * * * /usr/bin/dnsmaster-backup.sh
0 0 * * 5 /usr/bin/dns1.noor.net-backup.sh
0 1 * * 5 /usr/bin/dns0.noor.net-backup.sh
}}}
