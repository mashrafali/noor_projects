{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 30-1-2016
}}}
<<TableOfContents(3)>>

== How to migrate zimbra server from old version to a newer one ==
=== This Doc is still under testing from my side ===
=== 1. Introduction ===
Zimbra project doesn’t have a cross migration or proper account transfer documentation. All they tell is to do copy the folder /opt/zimbra to your new servers. But if any of those files infected with a rootkit or other malicious scripts , then your new server also will be compromised. So never sync or copy the entire directory of your zimbra installation. Zimbra also tell you to upgrade your production server to the latest version before migration. But improper upgrade may result in entire data lose. With this procedure you can do:

 * Migrate zimbra from one Operating System To another.
 * Migrate zimbra account between any hardware and Operating systems configurations.
 * No interruption on production server like software upgrade or service disable.
 * Migrate zimbra from old version to a new version server
 * Zimbra cross migrations without copying entire directories.

=== 2. Requirement ===
You need an old server with zmibra account and a new fresh server with the Os you wish. Dont’ create or make any custom configuration or setting in you new server. Please make sure to set your new servers hostname same as the old one.

 * Old server
  * Need ssh root login
  * Need zimbra admin logins
  * Enough HDD space to store backups
 * New server
  * Must be installed with latest stable zimbra
  * Need ssh root logins
  * Need zimbra admin logins
  * Enough HDD space to store backups

=== 3. Presetup ===
You need to setup an ssh key from the new server’s root account to the old server’s root account. Reduce the TTL of MX records of your domain to 500 seconds . So that you can easily switch the domain’s IP after migration. Please remember to schedule the migration task on non peek hours.

Create a directory in both new and old server into which we store all required files and data for doing the migration

{{{
[root@zimbra ~]# mkdir /backups/zmigrate
[root@zimbra ~]# chown zimbra.zimbra /backups/zmigrate
[root@zimbra ~]# su - zimbra
}}}
All operation in your Zimbra server must be performed as Zimbra user itself, otherwise you will get permission and ownership issues in your zimbra server

=== 4. Backup all data from Old server ===
==== 4.1 Find all domains ====
You need to find all the domains from your old server. We will store the domain list in a file called domains.txt. You need to back all the domains list as follows,

{{{
zimbra@zimbra:~$ cd /backups/zmigrate
zimbra@zimbra:/backups/zmigrate$ zmprov gad > domains.txt
zimbra@zimbra:/backups/zmigrate$ cat domains.txt
fun.com
myserver.com
justfortest.com
checkit.com
dieanotherday.com
gnutest.com
foo.com
zimbra.foo.com
zimbra@zimbra:/backups/zmigrate$
}}}
Now remove all domains and subdomains related with the main hostname of your server foo.com , from this list (domains.txt) because it was already created in your new server. So there is no need to create a new domain with the same name.

==== 4.2 Find all admin accounts ====
Most of these servers will have only one admin. But some servers have multiple admins. So it will be good to find all admin accounts. We will store the admins list in admins.txt

{{{
zimbra@zimbra:/backups/zmigrate$ zmprov gaaa > admins.txt
zimbra@zimbra:/backups/zmigrate$ cat admins.txt
admin@foo.com
zimbra@zimbra:/backups/zmigrate
}}}
==== 4.3 Find all email accounts ====
Next step is to find all the email accounts hosted in your old server. Get a list of your email accounts and save in the file emails.txt . So from this file we can see how many accounts that need to migrate.

{{{
zimbra@zimbra:/backups/zmigrate$ zmprov -l gaa >emails.txt
zimbra@zimbra:/backups/zmigrate$ cat emails.txt
gm@fun.com
forest@fun.com
galsync@fun.com
fax@myserver.com
paul@myserver.com
angela@myserver.com
brooke@myserver.com
hnmobile1@myserver.com
maria@justfortest.com
samantha@justfortest.com
backupmail@justfortest.com
admin@checkit.com
sandra@checkit.com
zimbra@zimbra:/backups/zmigrate$
}}}
Please remove all the email accounts from the file /backups/zmigrate/emails.txt with a starting words like spam, virus, ham, galsync . There is no need to restore these accounts. Even if you still need to restore , you can do it. I don’t like spam and virus emails.

==== 4.4 Get all distribution lists ====
You need to get all the distributions list and store it in a file called distributinlist.txt.

{{{
zimbra@zimbra:~$ zmprov gadl > /backups/zmigrate/distributinlist.txt
zimbra@zimbra:~$ cat /backups/zmigrate/distributinlist.txt
budgetrtodomainusers@fun.com
healthnowdomainusers@myserver.com
checkit.comdomainusers@checkit.com
northpointessdomainusers@dieanotherday.com
parkatnorthhillsdomainusers@gnutest.com
zimbra@zimbra:~$
}}}
==== 4.5 Get all members in distribution lists ====
In this step we are going to collect all members in each of these distributions. We will create a folder called distributinlist_members and create a file under this folder named distributinlist.txt , then store all the distributions members.

{{{
zimbra@zimbra:~$ mkdir /backups/zmigrate/distributinlist_members
zimbra@zimbra:~$ for i in `cat /backups/zmigrate/distributinlist.txt`; do zmprov gdlm $i > /backups/zmigrate/distributinlist_members/$i.txt ;echo "$i"; done
budgetrtodomainusers@fun.com
healthnowdomainusers@myserver.com
checkit.comdomainusers@checkit.com
}}}
==== 4.6 Find all email account’s passwords ====
Now need to find the encrypted password of all of your old email accounts and store it under a folder named userpass/ as follows:

{{{
zimbra@zimbra:/backups/zmigrate$ mkdir userpass
zimbra@zimbra:/backups/zmigrate$ for i in `cat emails.txt`; do zmprov  -l ga $i userPassword | grep userPassword: | awk '{ print $2}' > userpass/$i.shadow; done
}}}
==== 4.7 Backup all user names , Display names and Given Names ====
Zimbra will accept a Names and Disaplay names in email accounts during account creation. So we need to restore those data too. We will create a directory called userdata/ which contains these details of each of those email accounts

{{{
zimbra@zimbra:/backups/zmigrate$ mkdir userdata
zimbra@zimbra:/backups/zmigrate$ for i in `cat emails.txt`; do zmprov ga $i  | grep -i Name: > userdata/$i.txt ; done
}}}
==== 4.8 Now backup all email account ====
This will take some time to take backup of all email accounts. So you can run this command behind “screen”. A tgz file will be created with each emails name. We will use this files to transfer email accounts.

{{{
zimbra@zimbra:/backups/zmigrate$ for email in `cat /backups/zmigrate/emails.txt`; do for i in `cat ../emails.txt `; do zmmailbox -z -m $i getRestURL '/?fmt=tgz' > $i.tgz ;  echo $email ; done
gm@fun.com
forest@fun.com
galsync@fun.com
fax@myserver.com
fax2@myserver.com
paul@myserver.com
}}}
This tgz files contains

 * Mail
 * Contacts
 * Calendars
 * Briefcase
 * Tasks
 * Searches
 * Tags
 * Folders

All subfolders are included, except Junk and Trash. There is no way to include these in the big dump, but they can be exported separately:

==== 4.9 Now backup alias ====
Some times your server may have email aliases for certain accounts. So you need to copy those aliases too. We will create a sub folder called alias/ for storing the backup of Alias.

{{{
zimbra@zimbra:/backups/zmigrate$ mkdir -p alias/
zimbra@zimbra:/backups/zmigrate$ for i in `cat emails.txt`; do zmprov ga  $i | grep zimbraMailAlias |awk '{print $2}' > alias/$i.txt ;echo $i ;done
gm@fun.com
forest@fun.com
}}}
Some of your email accounts don’t have alias. So the above created files may be an empty file. Remove those empty files as follows,’\

{{{
zimbra@zimbra:/backups/zmigrate$ find alias/ -type f -empty | xargs -n1 rm -v
}}}
==== 4.10 Rsync folder to new server ====
Now we have all the required data to do the migration process. As a summery :

 * /backups/zmigrate – Have all the backups stored
 * /backups/zmigrate/domains.txt – Contains the domains names
 * /backups/zmigrate/emails.txt – Contains the list of email accounts
 * /backups/zmigrate/distributinlist.txt – Contains the distribution lists
 * /backups/zmigrate/distributinlist_members – Contains the members in each of your distributions
 * /backups/zmigrate/userpass – Contains the encrypted password of your email accounts
 * /backups/zmigrate/userdata – containts the email accounts user informations
 * /backups/zmigrate/alias – Contains all the aliases of your email accounts

Also the parent folder /backups/zmigrate contains a lot of zip file which are the data inside emails.

Now rsync the files as follows,

{{{
root@newserver # rsync -avp -e 'ssh -p 22' root@old-server-ip:/backups/zmigrate /backups/
}}}
=== 5. Restore in new server ===
So after finishing the rsync process , we need to restore this in your new server as follows:

All this operations must be carried out as zimbra sudo user itself. Don’t use root account to store the backups

{{{
[root@zimbra ~]# su - zimbra
[zimbra@zimbra]$
}}}
==== 5.1 Restore all domains ====
Now create all the domains that we have from the file /backups/zmigrate/domains.txt

{{{
[zimbra@zimbra zmigrate]$ for i in `cat /backups/zmigrate/domains.txt `; do  zmprov cd $i zimbraAuthMech zimbra ;echo $i ;done
2c86f244-de9d-4b7c-8e22-2246a8256219
myserver.com
dbf75058-d85e-4d60-8b69-1f148a456eb6
justfortest.com
ee90ffa2-505d-449f-82fd-129acb21cb5e
checkit.com
8b6bf287-f61e-4930-ada0-96b817292556
dieanotherday.com
17d3c73c-14f7-43aa-9fd2-c9be9e29c9e5
}}}
You can also verify the domains created from the zimbra admin panel too

==== 5.2 Create email accounts and set the old password ====
We need to create the email accounts for storing the mails. We also need to set the old passwords too. We already collected the account info and passwords.

To Create email accounts and restore passwords . Please use the following script to create it

{{{#!/bin/bash
#Scrit  for creating the email accounts createacct.sh
USERPASS="/backups/zmigrate/userpass"
USERDDATA="/backups/zmigrate/userdata"
USERS="/backups/zmigrate/emails.txt"
for i in `cat $USERS`
do
givenName=$(grep givenName: $USERDDATA/$i.txt | cut -d ":" -f2)
displayName=$(grep displayName: $USERDDATA/$i.txt | cut -d ":" -f2)
shadowpass=$(cat $USERPASS/$i.shadow)
tmpPass="CHANGEme"
zmprov ca $i CHANGEme cn "$givenName" displayName "$displayName" givenName "$givenName"
zmprov ma $i userPassword "$shadowpass"
done
}}}
==== 5.3 Restore email accounts ====
Now we are going to restore the emails from the Zip file. This process may take some hours. So it will be good to run behind “screen” command.

{{{
[zimbra@zimbra zmigrate]$ for i in `cat /backups/zmigrate/emails.txt`; do zmmailbox -z -m $i postRestURL "/?fmt=tgz&resolve=skip" /backups/zmigrate/$i.tgz ;  ; echo "$i -- finished "; done
gm@fun.com -- finished
forest@fun.com -- finished
}}}
==== 5.4 Now recreate the distribution lists ====
It is time to recreate all the distribution lists as follows.

{{{
[zimbra@zimbra zmigrate]$ for i in `cat distributinlist.txt`; do zmprov cdl $i ; echo "$i -- done " ; done
2a852fd8-6e66-426e-a76d-15192536042a
budgetrtodomainusers@fun.com -- done
a0f6ddb3-8525-4194-9397-6cf0a920dda6
}}}
==== 5.5 Restore the distribution lists ====
After creating the distribution lists we need to add all the members inside the distribution lists. We have the distribution lists in the folder distributinlist_members/ and the list is in distributionlist.txt file. Please use the following small script to restore the distribution lists.

{{{#!/bin/bash
there is typo mistake in the below script in dist list

[zimbra@zimbra zmigrate]$ cat restoredist.sh
# add all memebers to each of these distribution lists
for i in `cat distributinlist.txt`
do
        for j in `grep -v '#' distributinlist_members/$i.txt |grep '@'`
        do
        zmprov adlm $i $j
        echo " $j member has been added to list $i"
        done

done
}}}
==== 5.6 Restore Alias accounts ====
Please use the following script to restore alias. This will add all the aliases in your email accounts.

{{{#!/bin/bash
for i in `cat /backups/zmigrate/emails.txt`
do
        if [ -f "alias/$i.txt" ]; then
        for j in `grep '@' /backups/zmigrate/alias/$i.txt`
        do
        zmprov aaa $i $j
        echo "$i HAS ALIAS $j --- Restored"
        done
        fi
done
}}}
=== 6. Conclusion ===
So now we migrated all our email accounts. It is time for DNS change. You need to shut down the old zimbra services and change the DNS. After that send some test emails and make sure everything is working fine. Next step is to secure your zimbra server. You need to install ssl certificates and firewall in your new zimbra server. Now you have a new server with new packages and files with the same old email accounts and its data.

=== 7. References ===
{{{
https://wiki.zimbra.com/wiki/Zmprov_Examples
https://wiki.zimbra.com/wiki/Zmprov
https://wiki.zimbra.com/wiki/Backing_up_and_restoring_Zimbra_%28Open_Source_Version%29
'https://xmission.com/blog/2015/04/30/zimbra-server-admin-tip-mailbox-password-migration-and-server-settings-comparison'
http://stdout.no/zimbra-open-source-backup-strategy-and-scripts/
}}}




----------

== 8. You can Migrate Mailboxes with the following builtin zimbra tool ==

== Preparing the zmztozmig.conf file ==
Edit the '''zmztozmig.conf''' file in '''/opt/zimbra/conf''' on the destination server to configure the import settings. Account data, including email messages, attachments, contacts, calendar, tasks and briefcase folders for accounts are imported as individual account tgz tar files. '''Content of the Junk and Trash folders are not imported.'''

The following information is configured in the file:

 * Login information of the source server
 * Login information of the destination server
 * Domain mapping from source server to destination server
 * Account import details, including which accounts’ to import from and what type of information should be imported
 * Record keeping information, including log directory, whether to keep the tar files after migration, directory for failed migration files
 * How to resolve account import conflicts ('''resolve''')
 * Number of mailboxes to import simultaneously ('''thread''')
 * (Optional) Changes to ZimbraMailTransport that may be required

Following is a description of the parameters that are edited in the '''zmztozmig.conf''' file.

||<tablestyle="box-sizing: border-box; border-spacing: 0px; max-width: 100%; font-family: 'Source Sans Pro', Helvetica, Arial, sans-serif; font-size: 16px; line-height: 22.2222px; color: black; width: 1144.44px; background: rgb(240, 240, 240);"tablestyle="box-sizing: border-box;"rowstyle="box-sizing: border-box;"style="box-sizing: border-box; padding: 0px;">'''Parameter'''||<style="box-sizing: border-box; padding: 0px;">'''Description'''||<style="box-sizing: border-box; padding: 0px;">'''Entered as'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''SourceZCSServer'''||<style="box-sizing: border-box; padding: 0px;">IP address or name of the source server.||<style="box-sizing: border-box; padding: 0px;">'''SourceZCSServer=<source.com>'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''SourceAdminUser'''||<style="box-sizing: border-box; padding: 0px;">ZCS administrator name for the source server.||<style="box-sizing: border-box; padding: 0px;">'''SourceAdminUser=<sourceadmin>'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''SourceAdminPwd'''||<style="box-sizing: border-box; padding: 0px;">ZCS administrator password for the source server.||<style="box-sizing: border-box; padding: 0px;">'''SourceAdminPwd=<adminpswrd>'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''SourceAdminPort'''||<style="box-sizing: border-box; padding: 0px;">Source server admin port - 7071||<style="box-sizing: border-box; padding: 0px;">'''SourceAdminPort=<port>'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''TargetZCSServer'''||<style="box-sizing: border-box; padding: 0px;">IP address or name of the destination server where the data is imported.||<style="box-sizing: border-box; padding: 0px;">'''TargetZCSServer=<destination.com>'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''TargetAdminUser'''||<style="box-sizing: border-box; padding: 0px;">ZCS administrator name for the destination server.||<style="box-sizing: border-box; padding: 0px;">'''TargetAdminUser=<targetadmin>'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''TargetAdminPwd'''||<style="box-sizing: border-box; padding: 0px;">ZCS administrator password for the destination server.||<style="box-sizing: border-box; padding: 0px;">'''TargetAdminPwd=<adminpswrd>'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''TargetAdminPort'''||<style="box-sizing: border-box; padding: 0px;">Destination server admin port- 7071.||<style="box-sizing: border-box; padding: 0px;">'''TargetAdminPort=<port>'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''Threads'''||<style="box-sizing: border-box; padding: 0px;">Set this at a low number of threads. When you start to import the data, review the source/destination ZCS server CPU usage I/O rate and write to disk per second. If the server has power to run more threads, you can edit the '''zmztozmig.conf''' file to increase the threads one at a time. ||<style="box-sizing: border-box; padding: 0px;">'''Threads=<n>'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''WorkingDirectory'''||<style="box-sizing: border-box; padding: 0px;">The directory path to where the tar’d account data files are downloaded.||<style="box-sizing: border-box; padding: 0px;">'''WorkingDirectory=/opt/zimbra/data/zmztozmig/work'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''FailedDirectory'''||<style="box-sizing: border-box; padding: 0px;">The directory path to where tar’d account data files are moved if the account import fails.||<style="box-sizing: border-box; padding: 0px;">'''FailedDirectory=/opt/zimbra/data/zmztozmig/failed'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''KeepSuccessFiles'''||<style="box-sizing: border-box; padding: 0px;">If you want to keep the downloaded tar files after they are imported, set this to TRUE. ||<style="box-sizing: border-box; padding: 0px;">'''KeepSuccessFiles=FALSE'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''SuccessDirectory'''||<style="box-sizing: border-box; padding: 0px;">If you set '''KeepSuccessFiles''' to TRUE, identify the directory where tar’d account files are moved after successful imported.||<style="box-sizing: border-box; padding: 0px;">'''SuccessDirectory=/opt/zimbra/data/zmztozmig/successes'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''LogDirectory'''||<style="box-sizing: border-box; padding: 0px;">Configure the directory location where log files are saved. ||<style="box-sizing: border-box; padding: 0px;">'''LogDirectory=/opt/zimbra/data/zmztozmig/logs'''||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''DomainMap'''||<style="box-sizing: border-box; padding: 0px;">You can create multiple DomainMap entries if the Accounts list contains accounts from different domains. ||<style="box-sizing: border-box; padding: 0px;">'''DomainMap=fromdomain.com todomain.com'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''Accounts'''||<style="box-sizing: border-box; padding: 0px;">If data from all accounts on the source server should be imported, enter '''all'''. The Domains parameter must be set to list the domain from which the files are being imported. ||<style="box-sizing: border-box; padding: 0px;">'''Or''' '''Accounts=all''' ||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''Domains'''||<style="box-sizing: border-box; padding: 0px;">When the Accounts parameter is set to '''all''', identify the domains.||<style="box-sizing: border-box; padding: 0px;">'''Domains=domain1.example.com, domain2.example.com'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''Types'''||<style="box-sizing: border-box; padding: 0px;">To import all content, do not set any values for “types” Comment it out. ||<style="box-sizing: border-box; padding: 0px;">Types are: ||
||<rowstyle="box-sizing: border-box; background: white;"style="box-sizing: border-box; padding: 0px;">'''Resolve'''||<style="box-sizing: border-box; padding: 0px;">Only one value at a time can be set. Values: ||<style="box-sizing: border-box; padding: 0px;">'''Resolve=skip'''||
||<rowstyle="box-sizing: border-box; background-image: initial; background-attachment: initial; background-size: initial; background-origin: initial; background-clip: initial; background-position: initial; background-repeat: initial;"style="box-sizing: border-box; padding: 0px;">'''ZimbraMailTransport'''||<style="box-sizing: border-box; padding: 0px;">Include this entry if you want to change the '''ZimbraMailTransport''' to some other MTA. ||<style="box-sizing: border-box; padding: 0px;">'''ZimbraMailTransport=smtp:mta.zcs.mail.mydomain.com'''||


== Example of zmztozmig.conf ==
An example of the zmztozmig.conf file looks like:

{{{
#Source ZCS server IP/name,admin user name and password, server port
SourceZCSServer=192.168.211.20
SourceAdminUser=admin
SourceAdminPwd=PASSADMIN
SourceAdminPort=7071
#Destination/Target ZCS server IP/name,admin user name and password, server port
TargetZCSServer=192.168.211.129
TargetAdminUser=admin
TargetAdminPwd=PASSADMIN
TargetAdminPort=7071
Threads=3
WorkingDirectory=/tmp/ztozmig/mailboxdumps/
FailedDirectory=/tmp/ztozmig/mailboxfailures/
SuccessDirectory=/tmp/ztozmig/successes/
LogDirectory=/opt/zimbra/log/ztozmiglogs
KeepSuccessFiles=FALSE
Domains=zimbra.local
Accounts=all
}}}
== Import Account Data to the New Zimbra Server ==
Run the zmztozmig migration tool on the destination server as zimbra.

'''/opt/zimbra/libexec/zmztozmig –f /opt/zimbra/conf/zmztozmig.conf'''

The data on the source server is zipped and imported to the destination server. If you configured '''KeepSuccessFiles''' to FALSE, the tgz file is deleted by default once the account data is imported.

A common log file, ztozlog*.log, is created for the complete process and separate log files are created for data of each account that is imported. These should be saved until the new account data has been verified.

This is the end of the import process. An overview of the migration displays:

{{{
[INFO|main:1| 03/18/2015 20:34:57]: ****************SUMMARY**************************
[INFO|main:1| 03/18/2015 20:34:57]: Total Accounts processed         :    7
[INFO|main:1| 03/18/2015 20:34:57]: Successfull Accounts             :    7
[INFO|main:1| 03/18/2015 20:34:57]: Failed accounts                  :    0
[INFO|main:1| 03/18/2015 20:34:57]: Total Migration Time(seconds)    :    157.949
[INFO|main:1| 03/18/2015 20:34:57]: *************************************************
}}}
