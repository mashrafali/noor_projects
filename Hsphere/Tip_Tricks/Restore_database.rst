{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : restore Hsphere Database
Creation date: 2015-02-16
Latest update: 2015-02-16
}}}
 . <<TableOfContents(4)>>

== Restore Mysql files from Backup ==


1- Go to mysql server then know current mysql passowrd

{{{
web2.noor.com

[root@web2 ~]# cat ~mysql/.my.cnf
[client]
socket=/var/lib/mysql/mysql.sock
user=root
password=
}}}

2- Export All database before restore as a Backup:
{{{
	# mysqldump -u root -p --all-databases > ./mysql-backup/all-database_2015-02-16.sql
}}}

3- Backup current mysql database before restore it from backup :

{{{
Dump:
	# mysqldump -Q -uroot -p database >  ./mysql-backup/database_2015-02-16.sql
}}}

Copy Backup current mysql :
{{{
# cp -r /var/lib/mysql/database ./mysql-backup/database.mysql.original
}}}

4- Copy mysql from backup to new server
{{{
scp -r /mnt/nfs/2015-01-02/var/lib/mysql/database ./mysql-backup/
}}}

5- Check permission
{{{
:# ls -ll /var/lib/mysql/database
	-rw-rw---- 1 mysql mysql    8670 May 29 18:51 admin.frm
}}}

6- Replace database

{{{
Shut mysql
--------------
[root@web2 mysql-backup]# /etc/init.d/mysql stop
Shutting down MySQL...[  OK  ]
}}}

{{{

compare data:
-------------

ls -l  /var/lib/mysql/database | wc -l
}}}

{{{
Empty old database
-------------------
rm -rf /var/lib/mysql/mtadmin_b2b/*
}}}

{{{
Replace database
-------------------
 # scp -r /mnt/nfs/2015-01-02/var/lib/mysql/database/* /var/lib/mysql/database/
}}}


7- Change permissions

{{{
 # chown mysql: -R /var/lib/mysql/database/

 # chmod g+w  -R  /var/lib/mysql/database/

 # ls -ll /var/lib/mysql/mtadmin_b2b/
}}}



8- Reload Mysql:
{{{
  # /etc/init.d/mysql start
}}}

9- TEST Web Site
