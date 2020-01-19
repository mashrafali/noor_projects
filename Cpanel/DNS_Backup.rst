{{{
Author       : Ayman Tohamy
Title        : Backup cPanel DNS only 
Version      : 1.1
Creation date: 2015-10-13
Latest update: 2015-10-13
}}}
<<TableOfContents(4)>>

The below script will backup our shared hosting cPanel DNS only configurations and DNS zone files.


1- Create Backup Directory at Backup server

{{{
	backup-dc:~# mkdir /Backup/Datacenter/Hosting/41.187.100.26
	backup-dc:~# mkdir /Backup/Datacenter/Hosting/41.187.100.27
}}}

2- Copy public key from DNS servers to Backup server :

create RSA key
{{{
	ns1# ssh-keygen -t rsa
	ns1#scp /root/.ssh/id_rsa.pub root@172.17.13.56:/tmp
}}}

Import it to Backup server authorized keys: 
{{{
	backup-dc:~# cat /tmp/id_rsa.pub >>/root/.ssh/authorized_keys
}}}

3- Prepare script

{{{
# vi /usr/bin/dns-backup.sh
}}}

{{{
# Create backup directory with today date
date=$(date +"%Y-%m-%d")

mkdir -p /backup/$date
mkdir -p /backup/$date/zones


# synch bind config and zone files
scp -r /etc/named*  /backup/$date
scp -r /var/named/*  /backup/$date/zones

# compress backup
tar -zcf /backup/$date.tar.gz /backup/$date

# Copy backup to backup server
scp -r /backup/$date.tar.gz root@172.17.13.56:/Backup/Datacenter/Hosting/41.187.100.26

# remove backup directory
rm -rf /backup/$date

# remove backup from  server every period.
find /backup/ -type f -mtime +8 -exec rm {} \;
}}}


4- Make file excutable:

{{{
	# chmod +x  /usr/bin/dns-backup.sh
}}}

3- insert the script into the crontab to run it first day of every month :

{{{
	# crontab -e
}}}
{{{
	0 0 * * *        /usr/bin/dns-backup.sh
	
}}}

4- TEST :
{{{
	# /usr/bin/dns-backup.sh
}}}

5- Schedule Empty Backup at Backup server

{{{
00 00 * * *      find /Backup/Datacenter/Hosting/41.187.100.26/ -type f -mtime +30 -exec rm {} \;
}}}
