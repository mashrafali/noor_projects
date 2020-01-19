{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-10-01
Latest update: 2013-10-01
}}}
<<TableOfContents(4)>>

__'''Example'''__

 * Master : 192.168.0.80
 * Client: 192.168.0.44    or   217.139.224.32

= Setup NFS Server =
1—Download the Required nfs programs.

{{{
 apt-get install nfs-kernel-server portmap
}}}
{{{
    yum install nfs-utils nfs-utils-lib
}}}
2- Create NFS directory First, we need to create the NFS directory:

{{{
    mkdir -p /backup/nfs/
}}}
3- change the ownership of the directory to the user, nobody and the group, no group. These represent the default user through which clients can access a directory shared through NFS. Go ahead and chown the directory:

{{{
    chown nobody:nogroup /backup/nfs
}}}
4- export the directories to the other VPS:

{{{
    vi /etc/exports
}}}
Add the following lines to the bottom of the file, sharing both directories with the client:

{{{
    #/home                 12.33.44.555(rw,sync,no_root_squash,no_subtree_check)
    #/Backup/nfs            41.187.101.69(rw,sync,no_subtree_check,no_root_squash)
    /backup/nfs            192.168.0.44(rw,sync,no_subtree_check,no_root_squash)
    /Backup/nfs            217.139.224.32(rw,sync,no_subtree_check,no_root_squash)
}}}
 . These settings accomplish several tasks:

 * __rw: __This option allows the client server to both read and write within the shared directory
 * __sync:__ Sync confirms requests to the shared directory only once the changes have been committed.
 * __no_subtree_check:__ This option prevents the subtree checking. When a shared directory is the subdirectory of a larger filesystem, nfs performs scans of every directory above it, in order to verify its permissions and details. Disabling the subtree check may increase the reliability of NFS, but reduce security.
 * __no_root_squash: __This phrase allows root to connect to the designated directory run the following command to export them:

{{{
    exportfs -a
}}}
= Setup the NFS Client =
1- install the nfs programs.

{{{
 apt-get install nfs-common portmap         --> Ubuntu

    yum install nfs-common portmap        --> Centos

    yum install nfs-utils nfs-utils-lib        --> Centos
}}}
2- create the directories that will contain the NFS shared file:

{{{
    mkdir -p /mnt/nfs/
}}}
3- Start Client service

{{{
        [root@cp nfs]#  chkconfig nfs on
        [root@cp nfs]#  chkconfig nfslock on
        [root@cp nfs]#  chkconfig portmap on
                        or
         [root@cp nfs]#  chkconfig rpcbind on

        [root@cp nfs]#  service portmap restart
                        or
        [root@cp nfs]#  service rpcbind restart
        [root@cp nfs]#  service nfs restart
        [root@cp nfs]#  service nfslock restart
}}}
== NFS Client mount ==
 * A- using Comand-Line
 * B- using FSTAB
 * C- Using autofs

=== Mount using Command line ===
1- Mount

{{{
showmount -e nfs-server
showmount -e 192.168.0.80

mount nfs-server:/backup/nfs /mnt/nfs/
mount 192.168.0.80:/backup/nfs /mnt/nfs/

mount -o v3 192.168.0.80:/backup/nfs /mnt/nfs/
}}}
2- test Mount:

{{{
    df -h


    Filesystem            Size  Used Avail Use% Mounted on
    /dev/sda5              43G  4.6G   37G  12% /
    tmpfs                 1.9G     0  1.9G   0% /dev/shm
    /dev/sda1             504M   43M  436M   9% /boot
    /dev/sda2             2.0G   68M  1.9G   4% /tmp

    192.168.0.80:/backup/nfs

                      7.3T  1.4T  5.5T  20% /mnt/nfs/
}}}
{{{
    root@cp [~]# mount
}}}
{{{
    /dev/sda5 on / type ext4 (rw,usrjquota=quota.user,jqfmt=vfsv0)
    proc on /proc type proc (rw)
    sysfs on /sys type sysfs (rw)
    devpts on /dev/pts type devpts (rw,gid=5,mode=620)
    tmpfs on /dev/shm type tmpfs (rw,rootcontext="system_u:object_r:tmpfs_t:s0")
    /dev/sda1 on /boot type ext3 (rw)
    /dev/sda2 on /tmp type ext3 (rw,noexec,nosuid)
    none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
    /tmp on /var/tmp type none (rw,noexec,nosuid,bind)

    192.168.0.80:/backup/nfs on /mnt/nfs/ type nfs (rw,vers=4,addr=192.168.0.80,clientaddr=192.168.0.44)
}}}
 . 4- In mount not working input/output error from NFS client on CentOS this error even good Linux Admins make from time to time, here’s the soltuion(s) 1) check that portmap start is running
 {{{
service portmap start
}}}
 {{{
chkconfig portmap on
}}}
 once started try the mount again.
 . 5- Test copy files :

{{{
    root@cp [~]# touch /mnt/nfs//test.txt
}}}
{{{
    root@backup:~# ls -ll /backup/nfs/

    -rw-r--r-- 1 nobody nogroup 0 Sep 25 09:01 test.txt
}}}
=== Mount On Boot (Alwayas Active) ===
 . 1 - vi /etc/fstab

{{{
# Working locaaly only but not working when enable Mount NFS at Cpanel Backup configurations
#server:/usr/local/pub    /pub   nfs    rsize=8192,wsize=8192,timeo=14,intr

192.168.0.80:/backup/nfs    /mnt/nfs  nfs    rsize=8192,wsize=8192,timeo=14,intr

# Working when enble NFS Mount at Cpanel Backup configurations
192.168.0.80:/backup/nfs  /mnt/nfs   nfs     auto,noatime,nolock,bg,nfsvers=3,intr,tcp,actimeo=1800 0 0
}}}
 . 2 - you can use a single command to mount directories specified in the fstab file: [mount -a]

{{{
        root@cp [~]# df -h

        Filesystem            Size  Used Avail Use% Mounted on
        /dev/sda5              43G  4.6G   37G  12% /
        tmpfs                 1.9G     0  1.9G   0% /dev/shm
        /dev/sda1             504M   43M  436M   9% /boot
        /dev/sda2             2.0G   68M  1.9G   4% /tmp
}}}
{{{
        mount -a
}}}
 . 3- You can check the mounted directories with the two earlier commands:

{{{
 root@cp [~]# df -h

        Filesystem            Size  Used Avail Use% Mounted on
        /dev/sda5              43G  4.6G   37G  12% /
        tmpfs                 1.9G     0  1.9G   0% /dev/shm
        /dev/sda1             504M   43M  436M   9% /boot
        /dev/sda2             2.0G   68M  1.9G   4% /tmp

        192.168.0.80:/backup/nfs

                              7.3T  1.4T  5.5T  20% /mnt/nfs/
}}}
 . 4- You can mount -a at Start-up
  . {{{
        crontab -e
        -----
        @reboot         mount -a
        -----
}}}
 5- TEST again after reboot
  . {{{
        df -h
        mount
}}}

=== Auto Mount(autofs) ===
 . Autofs consults the master map configuration file /etc/auto.master to determine which mount points are defined.
  . the /etc/auto.misc file might define mount points in the /misc directory; this relationship would be defined in the /etc/auto.master file.
 Each entry in auto.master has three fields.: - The first field is the mount point. - The second field is the location of the map file. - The third field is optional, the third field can contain information such as a timeout value. 1- vi /etc/auto.master
 {{{
    /misc   /etc/auto.misc --timeout 60
}}}
 2- Add the following line to /etc/auto.misc:

 . {{{
    # myproject  -rw,soft,intr,rsize=8192,wsize=8192 penguin.example.net:/proj52
    nfs_test  -fstype=nfs,rw,soft,intr,rsize=8192,wsize=8192 192.168.0.80:/backup/nfs
}}}
  . The first field in /etc/auto.misc is the name of the /misc subdirectory. This directory is created dynamically by automount. It should not actually exist on the client machine. The second field contains mount options such as rw for read and write access. The third field is the location of the NFS export including the hostname and directory. Note
   .

-----
 . The directory /misc must exist on the local file system. There should be no subdirectories in /misc on the local file system. Autofs is a service. To start the service, at a shell prompt, type the following commands:
  . {{{
        /sbin/service autofs restart
}}}
 To view the active mount points, type the following command at a shell prompt:
 {{{
        /sbin/service autofs status
}}}
 If you modify the /etc/auto.master configuration file while autofs is running, you must tell the automount daemon(s) to reload by typing the following command at a shell prompt:
 {{{
        /sbin/service autofs reload
}}}

== unmount ==
 . - To Unmount:
 {{{
        umount /mnt/nfs/backup/nfs
}}}

= Restart NFS Server service =
{{{
      service portmap restart
      service nfs-kernel-server restart
}}}
= Backup Cpanel to NFS =
== Backup Configurations ==
__'''1- Create Backup directory at Backup server : '''__

We will take HPD as example

{{{
    mkdir /Backup/nfs/41.187.101.108
}}}
__'''2- Check Cpanel account size '''__

At server Check Account :

{{{
cpanel# du -hs /home/hpdadmin/
575M    /home/hpdadmin/
}}}
Note:

 . You should have free space = 3 * /home size for local backup but we will backup to NFS (/mnt/nfs)

__'''3- Enable Backup configuration '''__

From Cpanel - WHM Home - Backup - Backup configurations

 * __Backup - Status__ : Enable backup
 * __Backup Type__ : Compressed

 . {{attachment:cpanel-backup-1.jpg||height="538",width="958"}}

 * __Backup Schedule:__ Monthly (15th)
 . retain Backup : 1 or what number of backup needed to be retained

 . {{attachment:cpanel-backup-2.jpg||height="538",width="958"}}

 * __Backup user Accounts__ --> Select cpanel account [i.e. hpdadmin]
 . {{attachment:cpanel-backup-3.jpg||height="522",width="928"}}

 . {{attachment:cpanel-backup-4.jpg||height="566",width="1005"}} <<BR>><<BR>>

 * __Configure Backup Directory : __
  . __Databases :__  Per Account and Entire MySQL Directory
  . * Local Directory: /mnt/nfs <<BR>>
  . * Mount Backup : Enable [ Enable it if you are using NFS]
 . {{attachment:cpanel-backup-5.jpg||height="510",width="905"}}
  .
  . . <<BR>>

{{{
Note:
 if you are using NFS , select Mount Backup directory
# Add below line as an example at /etc/fstab for client

172.17.13.56:/Backup/nfs/41.187.101.108  /mnt/nfs   nfs     auto,noatime,nolock,bg,nfsvers=3,intr,tcp,actimeo=1800 0 0
}}}
 .

 * '''Additional Directories'''
  * Destination Name : Backup-DC
  * Backup Directory : <<BR>>
  {{{
/Backup/nfs/41.187.101.108/
}}}
 . {{attachment:cpanel-backup-6.jpg||height="524",width="928"}}
  * Add SFTP Destination with below details:
  . Backup will copied to this directory after taken locally (Strongly recomended).
  . {{{
/Backup/nfs/41.187.101.108/
}}}
 . {{attachment:cpanel-backup-7.jpg||height="543",width="1016"}} <<BR>><<BR>>
 . . * Remote Host : 172.17.13.56
  .
  . * Remote Account : root
  . * Choose Key Authentication:<<BR>>Generate new Key -->

 . {{attachment:cpanel-backup-8.jpg||height="590",width="1050"}}

 . *  use RSA --> 1024  (Don't enter name to use default [/root/.ssh/id_rsa] )
  * do not enter passphrase.
 . {{attachment:cpanel-backup-9.jpg||height="528",width="936"}} <<BR>><<BR>>
  * copy public key to Backup server : <<BR>><<BR>>
 . {{{
cpanel#scp /root/.ssh/id_rsa.pub root@172.17.13.56:/tmp
}}}
  * Import it to Backup server authorized keys:
 . {{{
root@backup-dc:~# cat /tmp/id_rsa.pub >>/root/.ssh/authorized_keys
root@backup-dc:~#  chmod 700 ~/.ssh/authorized_keys
}}}
  * Save and validate>> to test Key Authentication--- > Done.

 . {{attachment:cpanel-backup-10.jpg}}

== Check Backup Schedule ==
by default Backup configuration run at 2:00 AM and you can edit cron tab to run it immediate:

Checkcrontab

{{{
crontab -e
0 2 * * * /usr/local/cpanel/bin/backup
}}}
== Run Backups Manually ==
To run a backup manually, enable daily backup as test then run the following command:

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
= Reference =
 . Auto Mount: http://www.centos.org/docs/4/4.5/System_Administration_Guide/Mounting_NFS_File_Systems-Mounting_NFS_File_Systems_using_autofs.html
