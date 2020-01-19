{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-11-05
Latest update: 2014-11-05
}}}
<<TableOfContents(4)>>

== Moving Hsphere MySQL Accounts ==
'''WARNING:'''''' '''The undermentioned procedure is recommended for experienced Parallels H-Sphere owners only!

All MySQL resources of the particular Parallels  H-Sphere account are called MySQL account hereinafter. The following  steps explain how to move all databases of a particular Parallels H-  Sphere account to a new logical MySQL server and apply changes to the  Parallels H-Sphere database.

To move MySQL account:

 . 1- Log into the source MySQL server and get MySQL root password that will be generated after entering the following command: <<BR>>
 {{{
# cat ~mysql/.my.cnf
}}}

{{{
   ############
   web.noor.com
   #############
   #  cat ~mysql/.my.cnf
   [client]
   socket=/var/lib/mysql/mysql.sock
   user=root
   password=webpassword
}}}

 . Recommended to Backup all Web server databases
{{{ 
    # mysqldump -u root -p --all-databases > mysql-backup/new/all-database.sql
}}}


 2- Export user account databases on source MySQL server with the help of mysqldump utility:
 {{{
# mysqldump -Q -uroot -p DBNAME > DBNAME.sql
}}}
 where DBNAME is the database name.

This should be applied to every user database within the account.

{{{
    # mysqldump -Q -uroot -p pdadmin_bank >  mysql-backup/new/pdadmin_bank.sql
}}}


 . 3- Dump user database privileges on source MySQL server:
 {{{
# mysqldump -c -e -Q -t mysql -uroot -p db -w "db like 'USERNAME_%'" > USERNAME_mysql.db.sql
}}}
 where USERNAME is an Parallels H-Sphere user prefix for database.

{{{
# mysqldump -c -e -Q -t mysql -uroot -p db -w "db like 'pdadmin_%'" > mysql-backup/new/pdadmin_mysql_users.db.sql
}}}

 4- Log into CP server. Change MySQL logical server id for the account:<<BR>>
 {{{
   # su - cpanel
   # java -Xms64M -Xmx256M psoft.hsphere.tools.ChangeLServerId -a ACC_ID --from OLD_LID --to NEW_LID
}}}

 . where: ACC_ID - the account id OLD_LID - source logical mysql server ID NEW_LID - target mysql logical server ID

{{{
    ###########
    cp.noor.com
    ###########
    Domain     	     Account ID 	
    pdadmin 		31986
    --------------
   26 - mysql.noor.com
   37 - mysql2.noor.com
   -----------------------

   Change MySQL Logical server
   ----------------------------------
   # su - cpanel
   $ java -Xms64M -Xmx256M psoft.hsphere.tools.ChangeLServerId -a ACC_ID --from OLD_LID --to NEW_LID
   $ java -Xms64M -Xmx256M psoft.hsphere.tools.ChangeLServerId -a 31986 --from 26 --to 37

}}}


 5- Create empty databases on the target MySQL server: <<BR>><<BR>>

{{{
   # su - cpanel<<BR>>
   # java -Xms64M -Xmx256M psoft.hsphere.tools.PhysicalCreator -rg mysql -co -lid NEW_LID -accs ACC_ID
}}}

{{{
  Create empty Database
   ---------------------------
   # su - cpanel
   $ java -Xms64M -Xmx256M psoft.hsphere.tools.PhysicalCreator -rg mysql -co -lid NEW_LID -accs ACC_ID
   $ java -Xms64M -Xmx256M psoft.hsphere.tools.PhysicalCreator -rg mysql -co -lid 37 -accs 31986
   }}}


 . 6- Transfer all DBNAME.sql and USERNAME_mysql.db.sql files from the source server to the target MySQL server.
 
{{{
   Copy Bcakup:
   ############
   web ~]# scp -r mysql-backup/new root@41.187.100.10:/root/mysql-backup/

}}}


7- Log into the target MySQL server and get MySQL root password that will be generated after entering the following command: <<BR>>
 {{{
# cat ~mysql/.my.cnf
}}}

{{{
web2.noor.com
##############

[root@web2 ~]# cat ~mysql/.my.cnf
[client]
socket=/var/lib/mysql/mysql.sock
user=root
password=web2password

}}}


 8- Import databases:
 {{{
# mysql -uroot -p DBNAME < DBNAME.sql
}}}

{{{
   Import Database
   ---------------------
   # cd mysql-backup/new
   root@web2 mysql-backup/new]#

   # mysql -uroot -p  pdadmin_bank <  pdadmin_bank.sql
   # mysql -uroot -p  pdadmin_test4 <  pdadmin_test4.sql

}}}


 9- Restore user database privileges:
 {{{
# mysql -uroot -p mysql < USERNAME_mysql.db.sql
# mysqladmin reload -p
}}}

{{{
  Grant users:
  ----------------
   # mysql -uroot -p mysql < USERNAME_mysql.db.sql
   # mysql -uroot -p mysql < pdadmin_mysql_users.db.sql

   # mysqladmin reload -p
}}}



 10- Restarting Parallels H-Sphere Control Panel

{{{


To restart Parallels H-Sphere Control Panel:

    Log into the CP server as root.
    Run:

    Linux:

    /etc/rc.d/init.d/httpdcp stop
    killall -9 java
    sleep 10
    /etc/rc.d/init.d/httpdcp start
}}}


 . 11- Make  sure to check MySQL dbs functionality on the target server. If it is  ok, you may delete MySQl databases from the source server by running the  following commands:

{{{
         /hsphere/shared/scripts/mysql-drop-database DBNAME

         /hsphere/shared/scripts/mysql-delete-user USERNAME
}}}

{{{
/hsphere/shared/scripts/mysql-drop-database pdadmin_bank
/hsphere/shared/scripts/mysql-drop-database pdadmin_test4
}}}

Perform steps 2,3,8,9,11 for each MySQL db and user of the current Parallels H-Sphere account on the source MySQL server.

Change  Website's file which contains DB connection to point it to new database server.
