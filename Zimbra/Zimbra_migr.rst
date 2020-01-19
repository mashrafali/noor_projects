{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-01-02
Latest update: 2013-01-02
}}}
<<TableOfContents(4)>>

= Zimbra to Zimbra Migration =
== Introduction ==
 . Importing Data between Different Version of ZCS servers using the Zimbra to Zimbra Migration Tool
 Use the Zimbra to Zimbra migration tool '''zmztozmig '''to import account data from one Zimbra Collaboration Server to another Zimbra Collaboration Server that is running a different version of ZCS. This tool can be used to import files from ZCS 6.0.6 or later to the latest version of ZCS. The process of migrating accounts to a ZCS server running a later version of ZCS includes:
 .
 . 1- Provisioning the accounts on the destination server      before running zmztozmig
 2- Editing the '''zmztozmig.conf''' file in '''/opt/zimbra/conf'''      on the source server to configure the import settings
 . 3- Running '''zmztozmig '''to import the account’s data      to the destination server
 .   . When zmztozmig is used to import files, after the data is imported, the data in the accounts on the destination server are not an exact replica of the data in the accounts on the source server. And account preferences are not imported. Also, if the destination accounts do not have the same account IDs as they had on the source server, files that were shared and appointments related to the original sender might not work.
  '''Note:''' Use the '''zmmailboxmove''' command to move an account between two mailbox servers with the same LDAP master. '''Note:'''  includes a new '''zmmboxmove''' command that has enhanced functionality of zmmailboxmove to handle cross mailstore version migrations (but not across ldap instance like zmztozmig).
  .

== Preparing the zmztozmig.conf file ==
 . Edit the '''zmztozmig.conf''' file in '''/opt/zimbra/conf''' on the source server to configure the import settings.
 .
 . Account data, including email messages, attachments, contacts, calendar, tasks and briefcase folders for accounts are imported as individual account tgz tar files. Content of the Junk and Trash folders are not imported. The following information is configured in the file:

 * Login information of the source server
 * Login information of the destination server
 * Domain mapping from source server to destination server
 * Account import details, including which accounts’ to      import from and what type of information should be imported
 * Record keeping information, including log directory,      whether to keep the tar files after migration, directory for failed      migration files
 * How to resolve account import conflicts ('''resolve''')
 * Number of mailboxes to import simultaneously ('''thread''')
 * (Optional) Changes to !ZimbraMailTransport that may be      required
  . Following is a description of the parameters that are edited in the zmztozmig.conf file.

||<tablebgcolor="#F0F0F0" tablewidth="100%" tablestyle="mso-cellspacing:1.5pt;mso-yfti-tbllook:1184" tableclass="MsoNormalTable"rowstyle="mso-yfti-irow:0;mso-yfti-firstrow:yes"style="padding:.75pt .75pt .75pt .75pt">'''Parameter ''' ||<style="padding:.75pt .75pt .75pt .75pt">'''Description ''' ||<style="padding:.75pt .75pt .75pt .75pt">'''Entered as ''' ||
||<rowstyle="mso-yfti-irow:1"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''SourceZCSServer ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">IP address or name of   the source server. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''SourceZCSServer=<source.com>''' ||
||<rowstyle="mso-yfti-irow:2"style="padding:.75pt .75pt .75pt .75pt">'''!SourceAdminUser ''' ||<style="padding:.75pt .75pt .75pt .75pt">ZCS administrator name   for the source server. ||<style="padding:.75pt .75pt .75pt .75pt">'''!SourceAdminUser=<sourceadmin>''' ||
||<rowstyle="mso-yfti-irow:3"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!SourceAdminPwd ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">ZCS administrator   password for the source server. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!SourceAdminPwd=<adminpswrd>''' ||
||<rowstyle="mso-yfti-irow:4"style="padding:.75pt .75pt .75pt .75pt">'''!SourceAdminPort ''' ||<style="padding:.75pt .75pt .75pt .75pt">Source server admin   port - 7071 ||<style="padding:.75pt .75pt .75pt .75pt">'''!SourceAdminPort=<port>''' ||
||<rowstyle="mso-yfti-irow:5"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''TargetZCSServer ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">IP address or name of   the destination server where the data is imported. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''TargetZCSServer=<destination.com>''' ||
||<rowstyle="mso-yfti-irow:6"style="padding:.75pt .75pt .75pt .75pt">'''!TargetAdminUser ''' ||<style="padding:.75pt .75pt .75pt .75pt">ZCS administrator name   for the destination server. ||<style="padding:.75pt .75pt .75pt .75pt">'''!TargetAdminUser=<targetadmin>''' ||
||<rowstyle="mso-yfti-irow:7"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!TargetAdminPwd ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">ZCS administrator   password for the destination server. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!TargetAdminPwd=<adminpswrd>''' ||
||<rowstyle="mso-yfti-irow:8"style="padding:.75pt .75pt .75pt .75pt">'''!TargetAdminPort ''' ||<style="padding:.75pt .75pt .75pt .75pt">Destination server   admin port- 7071. ||<style="padding:.75pt .75pt .75pt .75pt">'''!TargetAdminPort=<port>''' ||
||<rowstyle="mso-yfti-irow:9"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''Threads ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">Set the number of   accounts to be imported simultaneously. The default is 1. Set this at a low   number of threads. When you start to import the data, review the   source/destination ZCS server CPU usage I/O rate and write to disk per   second. If the server has power to run more threads, you can edit the   zmztozmig.conf file to increase the threads one at a time. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''Threads=<n>''' ||
||<rowstyle="mso-yfti-irow:10"style="padding:.75pt .75pt .75pt .75pt">'''!WorkingDirectory ''' ||<style="padding:.75pt .75pt .75pt .75pt">The directory path to   where the tar’d account data files are downloaded. ||<style="padding:.75pt .75pt .75pt .75pt">'''!WorkingDirectory=/opt/zimbra/data/zmztozmig/work''' ||
||<rowstyle="mso-yfti-irow:11"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!FailedDirectory ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">The directory path to   where tar’d account data files are moved if the account import fails. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!FailedDirectory=/opt/zimbra/data/zmztozmig/failed''' ||
||<rowstyle="mso-yfti-irow:12"style="padding:.75pt .75pt .75pt .75pt">'''!KeepSuccessFiles ''' ||<style="padding:.75pt .75pt .75pt .75pt">'''!KeepSuccessFiles''' is set to FALSE. The tar files are deleted   after the data is imported. If you want to keep   the downloaded tar files after they are imported, set this to TRUE. ||<style="padding:.75pt .75pt .75pt .75pt">'''!KeepSuccessFiles=FALSE''' ||
||<rowstyle="mso-yfti-irow:13"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!SuccessDirectory ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">If you set '''!KeepSuccessFiles'''   to TRUE, identify the directory where tar’d account files are moved after   successful imported. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!SuccessDirectory=/opt/zimbra/data/zmztozmig/successes''' ||
||<rowstyle="mso-yfti-irow:14"style="padding:.75pt .75pt .75pt .75pt">'''!LogDirectory ''' ||<style="padding:.75pt .75pt .75pt .75pt">A common log file,   ztozlog*.log, is created for the complete import process and separate log   file are created for each account data file that is imported. Configure the   directory location where log files are saved. ||<style="padding:.75pt .75pt .75pt .75pt">'''!LogDirectory=/opt/zimbra/data/zmztozmig/logs''' ||
||<rowstyle="mso-yfti-irow:15"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!DomainMap ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">If accounts are being   migrated from one domain to another domain, specify the source domain and   destination domain. You can create   multiple DomainMap entries if the Accounts list contains accounts from   different domains. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''!DomainMap=fromdomain.com   todomain.com''' ||
||<rowstyle="mso-yfti-irow:16"style="padding:.75pt .75pt .75pt .75pt">'''Accounts ''' ||<style="padding:.75pt .75pt .75pt .75pt">Specify which   account’s data should be imported. This is a comma separated list of   accounts. If data from all   accounts on the source server should be imported, enter '''all'''. The Domains parameter   must be set to list the domain from which the files are being imported. ||<style="padding:.75pt .75pt .75pt .75pt">'''Accounts= test1@example.com , test2@example.com ''' '''Or''' '''Accounts=all''' ||
||<rowstyle="mso-yfti-irow:17"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''Domains ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">When the Accounts   parameter is set to '''all''', identify the domains. ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''Domains=domain1.example.com,   domain2.example.com''' ||
||<rowstyle="mso-yfti-irow:18"style="padding:.75pt .75pt .75pt .75pt">'''Types ''' ||<style="padding:.75pt .75pt .75pt .75pt">Specify the type of   data that should be imported. This is a comma separated list. To import all content,   do not set any values for “types” Comment it out. ||<style="padding:.75pt .75pt .75pt .75pt">'''Types=message,   conversation''' Types are ||
||<rowstyle="mso-yfti-irow:19"bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''Resolve ''' ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">Use Resolve to   determine how to resolve conflicts between the items being imported and the   items already in the destination account. Only one value at a   time can be set. Values: ||<bgcolor="white" style="padding:.75pt .75pt .75pt .75pt">'''Resolve=skip''' ||
||<rowstyle="mso-yfti-irow:20;mso-yfti-lastrow:yes"style="padding:.75pt .75pt .75pt .75pt">'''!ZimbraMailTransport ''' ||<style="padding:.75pt .75pt .75pt .75pt">Using '''!ZimbraMailTransport'''   is optional. Include this entry if   you want to change the '''!ZimbraMailTransport''' to some other MTA. ||<style="padding:.75pt .75pt .75pt .75pt">'''!ZimbraMailTransport=smtp:mta.zcs.mail.mydomain.com''' ||


== Provision Accounts on the Destination Server ==
 .  The accounts that will have their data imported must be provisioned on the destination server before you can import the files. See the Zimbra Collaboration Server Administration Guide for details about provisioning accounts.

== Import Account Data to the New Zimbra Server ==
'''Import Account Data to the New Zimbra Server'''

Run the zmztozmig migration tool on the source server as zimbra.

{{{
 /opt/zimbra/libexec/zmztozmig –f /opt/zimbra/conf/zmztozmig.conf
}}}
 . The data on the source server is zipped and imported to the destination server.

== check migrated mailbox ==

 . If you configured '''!KeepSuccessFiles''' to FALSE, the tgz file is deleted by default once the account data is imported.
 . A common log file, ztozlog*.log, is created for the complete process and separate log files are created for data of each account that is imported. These should be saved until the new account data has been verified. After you verify that the account data was imported correctly, you can delete the account on the source server.
