{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-09-30
Latest update: 2013-09-30
}}}
<<TableOfContents(3)>>

How to restore full cpanel backup or (.tar.gz) file from Cpanel WHM or using command line?

'''To Restore Backup using cPanel WHM'''

1. Login to the WHM using root password through web browser.

 2. To restore a full backup file, simply move the file (usually named as username.tar.gz or cpmove-username.tar.gz) to /home directory (or /usr/home, /web, /home2, /home3, /root, /usr directory if applicable).<<BR>>
 {{{
i.e.  #/home/elsewedy.tar.gz
}}}
 2. Click on Restore a full Backup/cpmove file as show in step (1).
 2. Now, Enter the username for the account you wish to restore step (2).
 2. Click on Restore in order to restore user as shown in step (3).
 2. {{attachment:cpanel-restore.png}}

'''<<BR>> Restore cPanel WHM user or single domain Backup through command line.<<BR>>'''

Alternatively, if you are the server administrator you can login to the server via SSH as root, and run the following command in the shell:

{{{
# /scripts/restorepkg username
# /scripts/restorepkg elsewedy
}}}
Where '''username''' is the name of the user that you want to restore.

 . You don’t have to create an account prior to restore as the account will be automatically created if it does not exist. All the user files and folders including databases will be restored to the correct locations. There is not need to untar or extract the backup file.
