{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-09-30
Latest update: 2013-09-30
}}}
<<TableOfContents(3)>>

== Cpanel Backup System ==
=== Backup Configuration ===
In 11.38, WHM offers a new interface to create backups. This new interface has several features that give system administrators the opportunity to customize the backup configuration.

=== Legacy Backup ===
In 11.38, WHM features an updated interface for the Backup Configuration screen in WHM's Backup section.

System administrators who may not feel comfortable with this update can still use the 11.36 version of Backup Configuration, formerly called Configure Backups now called Legacy Backup Configuration. For more information, read our documentation about the legacy backup.

From Cpanel - WHM Home - Backup - Legacy Backup

the same feature will be located at Legacy Backup configuration

To Force Backup from CLI run below command:

{{{
/scripts/cpbackup --force
}}}
Also it should be noted that you must disable  legacy backup and enable new backup system on every account! Otherwise  calling above command will do nothing (no accounts)

=== User Backup Selection ===
We added a new feature called Backup User Selection under the Backup section in WHM.

This feature allows system administrators to backup each account with the Legacy Backup Configuration, the new Backup Configuration, or both.

For example, a system administrator can configure user1 to use the Legacy Backup Configuration while user2 is configured to use the new Backup Configuration.

{{{
 Note: The new Backup Restoration feature will not restore backups saved with the Legacy Backup Configuration.

To enable new backing system go to: __Backup User  Selection Disable legacy backup__ and enable new backup system for  accounts. Then above command:
}}}
{{{
# /scripts/cpbackup --force
will start a full cPanel backup at any time, using the settings in /etc/cpbackup.conf (which are set in the WebHost Manager at Main >> Backup >> Configure Backup).
}}}
== Backup Configurations ==
__'''1- Create Backup directory at Backup server : '''__

{{{
    mkdir /Backup/Datacenter/Sewedy
    chmod -R 770 /Backup/Datacenter/Sewedy/
}}}
__'''2- Check Cpanel account size '''__

At server Check Account :

{{{
    root@mail [~]# du -hs /home/elsewedy/
    117G    /home/elsewedy/
}}}
Note:

 . You should have free space = 3 * /home size

__'''3- Enable Backup configuration '''__

From Cpanel - WHM Home - Backup - Backup configurations

 * __Backup - Status__ : Enable backup
 * __Backup Type__ : Compressed
 * __Backup Schedule:__ Monthly (15th)
 . retain Backup : 1 or what number of backup needed to be retained
  . {{attachment:cpanel-backup-1.jpg}}

 * __Backup user Accounts__ --> Select cpanel account [i.e. Sewedy - noor25 - wiki]
  . {{attachment:cpanel-backup-3.jpg}}
 * __Databases :__  Per Account and Entire MySQL Directory
  . {{attachment:cpanel-backup-2.jpg}}
 * __Configure Backup Directory : __
  . * Local Directory: /Backup <<BR>>
  . * Mount Backup : Disable [ Enable it if you are using NFS]
   . {{{
      Note:
 if we run Backup locally, we need to free space 3 time of /home ,
 so we can add NFS directory and mount it locally to server then change local backup directory to this NFS (i.e. /mnt/nfs instead of /backup)in order to save server disk space.
}}}
   http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Cpanel/Backup_NFS
   .
   . <<BR>>
{{{
Note:
 if you are using NFS , select Mount Backup directory
# Add below line as an example at /etc/fstab for client

192.168.0.80:/backup/nfs  /mnt/nfs   nfs     auto,noatime,nolock,bg,nfsvers=3,intr,tcp,actimeo=1800 0 0
}}}
    . {{attachment:cpanel-backup-4.jpg}}
 * '''Additional Directories'''
  * Destination Name : Backup-DC
  * Backup Directory : /Backup/Datacenter/Sewedy/
  * Add SFTP Destination with below details:
  . Backup will copied to this directory after taken locally (Strongly recomended).
  . {{{
/Backup/Datacenter/Sewedy/
}}}
  . * Remote Host : 172.17.13.56
  . * Remote Account : noor
   * Choose Key Authentication:<<BR>>Generate new Key -->
   . *  use RSA --> 1024  (Don't enter name to use default [/root/.ssh/id_rsa] )
    * do not enter passphrase.
    * copy public key to Backup server :
   . {{{
scp /root/.ssh/id_rsa.pub root@172.17.13.56:/tmp
}}}
    * Import it to Backup server authorized keys:
   . {{{
noor@ubuntuclient:/tmp$ cat /tmp/id_rsa.pub >>/home/noor/.ssh/authorized_keys
noor@ubuntuclient:/tmp$ chmod 700 ~/.ssh/authorized_keys
}}}
    * {{attachment:cpanel-backup-5.jpg}}
    * Save and validate>> to test Key Authentication--- > Done.

== Check Backup Schedule ==
by default Backup configuration run at 2:00 AM and you can edit cron tab to run it immediate:

Checkcrontab

{{{
crontab -e
0 2 * * * /usr/local/cpanel/bin/backup
}}}
== Run Backups Manually ==
To run a backup manually, run the following command:

{{{
/usr/local/cpanel/bin/backup
}}}
{{{
root@mail [~]# /usr/local/cpanel/bin/backup
info [backup] Started at Thu Oct  3 10:48:39 2013
info [backup] Backups are not scheduled to run today (This can be adjusted in WHM => Backup => Backup Configuration)
}}}
If the backup is up-to-date, and you want to run backups anyway, run:

{{{
/usr/local/cpanel/bin/backup --force
}}}
{{{
root@mail [~]# /usr/local/cpanel/bin/backup --force
info [backup] Started at Thu Oct  3 10:49:51 2013
info [backup] Validating remote transports
info [backup] Process started in background.
info [backup] Log file: /usr/local/cpanel/logs/cpbackup/1380790191.log
}}}
== Backup Log files ==
Backup Log files exist at  /usr/local/cpanel/logs/cpbackup/

{{{
cat  /usr/local/cpanel/logs/cpbackup/filename.log
tail -f /usr/local/cpanel/logs/cpbackup/filename.log
}}}
{{{
root@cp [~]# ls -ll  /usr/local/cpanel/logs/cpbackup/
                total 64

                -rw-------. 1 root root 3320 Sep 25 12:40 1380105601.log
                -rw-------. 1 root root 3644 Sep 25 13:51 1380109861.log
                -rw-------. 1 root root 3644 Sep 25 13:51 1380790191.log

cat /usr/local/cpanel/logs/cpbackup/1380790191.log
tail -f /usr/local/cpanel/logs/cpbackup/1380790191.log
}}}
== References: ==
http://docs.cpanel.net/twiki/bin/view/AllDocumentation/WHMDocs/BackupConfiguration
