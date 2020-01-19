{{{
Author       : Ashraf Ali
Version      : 1.0
Creation date: 2013-11-03
Latest update: 2014-12-18 (Ayman Tohamy)
}}}
<<TableOfContents(3)>>

= CBPolicyd =
== What is CBPolicyd ? ==
'''Policyd'''

Policyd v2 (codenamed "cluebringer") is a multi-platform policy server for popular MTAs. This policy daemon is designed mostly for large scale mail hosting environments. The main goal is to implement as many spam combating and email compliance features as possible while at the same time maintaining the portability, stability and performance required for mission critical email hosting of today. Most of the ideas and methods implemented in Policyd v2 stem from Policyd v1 as well as the authors' long time involvement in large scale mail hosting industry.

'''Old Policyd v1'''

Policyd v1 is an anti-spam plugin for Postfix (written in C) that does Greylisting, Sender-(envelope, SASL or host/ip)-based throttling (on messages and/or volume per defined time unit), Recipient rate limiting, Spamtrap monitoring/blacklisting, HELO auto blacklisting and HELO randomization prevention. Policyd v1 is only open for bug & security fixes, everyone is strongly advised to migrate to v2.


== Enabling and configuring CBPolicyd in Zimbra 8.0.0 ==


• Switch to the zimbra user

{{{
su - zimbra
}}}

• Enable the cbpolicyd service (server-wide) via zmprov:

{{{
zmprov ms servername +zimbraServiceInstalled cbpolicyd +zimbraServiceEnabled cbpolicyd
}}}

(mind that "+" in front of "zimbraServiceEnabled cbpolicyd". Miss it and you'll end up having cbpolicyd as the ONLY service enabled)

• In most recent Zimbra versions a CBpolicyD zimbraMtaRestriction exists. If not, add a new one:

{{{
	zimbra@mail:~$zmlocalconfig -e postfix_enable_smtpd_policyd=yes

    	zimbra@mail:~$zmprov mcf +zimbraMtaRestriction “check_policy_service inet:127.0.0.1:10031″

}}}

• Configure the CBPolicyd logging and active modules

{{{
zmlocalconfig -e cbpolicyd_log_level=4; zmlocalconfig -e cbpolicyd_log_detail=modules,tracking,policies; zmlocalconfig -e cbpolicyd_module_accesscontrol=1 cbpolicyd_module_checkhelo=1 cbpolicyd_module_checkspf=1 cbpolicyd_module_greylisting=1
}}}

(sets loglevel to Debug, detailed logging of Modules, Policies and Tracking, enables all the modules excluding Amavis)

{{{
zimbra@mail:~$ zmlocalconfig  | grep -i cbpolicy    
cbpolicyd_bind_port = 10031
cbpolicyd_bypass_mode = tempfail
cbpolicyd_bypass_timeout = 30
cbpolicyd_cache_file = ${zimbra_home}/data/cache
cbpolicyd_db_file = ${zimbra_home}/data/cbpolicyd/db/cbpolicyd.sqlitedb
cbpolicyd_log_detail = modules,tracking,policies
cbpolicyd_log_file = ${zimbra_log_directory}/cbpolicyd.log
cbpolicyd_log_level = 4
cbpolicyd_log_mail = main
cbpolicyd_module_accesscontrol = 1
cbpolicyd_module_checkhelo = 1
cbpolicyd_module_checkspf = 1
cbpolicyd_module_greylisting = 1
cbpolicyd_module_quotas = 1
cbpolicyd_pid_file = ${zimbra_log_directory}/cbpolicyd.pid
cbpolicyd_timeout = 120
}}}

• Restart the MTA service to apply all the configurations

{{{
zmmtactl restart
}}}

== Enabling and configuring CBPolicydGreylisting in Zimbra 8.6 ==

Defining a greylisting policy with cbpolicyd

Note: See the official wiki for an explanation of the parameters By default, the greylisting module is not enabled, and there are no greylisting policies. To enable greylisting, do the following as zimbra:

For Zimbra 8.6+, do
{{{
    zmprov ms `zmhostname` zimbraCBPolicydGreylistingEnabled TRUE
}}}

for older version of Zimbra, do the following instead:
{{{
    zmlocalconfig -e cbpolicyd_module_greylisting=1
}}}

ZCS8 only: If you want to start building your greylisting table without enforcing policy (I.e., train your server before enabling greylisting):

In Zimbra 8.6+, do:
{{{
    zmprov ms `zmhostname` zimbraCBPolicydGreylistingTrainingEnabled TRUE
}}}

for earlier 8.x versions, do:
{{{
    zmlocalconfig -e cbpolicyd_module_greylisting_training=1
}}}

When you are ready to have greylisting applied, set the value for cbpolicyd_module_greylisting_training to zero. 

== WebUI files ==


In Zimbra 8.0.0, download the package using the following command:

{{{
wget http://devlabs.linuxassist.net/attachments/download/230/cluebringer-snapshot-2.1.x-201205100639.tar.gz
}}}

Unpack the package using the following command:

{{{
tar xzvf cluebringer-snapshot-2.1.x-201205100639.tar.gz
}}}

Change directory to '''cluebringer-snapshot-2.1.x-201205100639'''. You will see that it contains .php and .css files. Copy these files to '''/opt/zimbra/cbpolicyd/share/webui/ directory'''.


== Initializing the database ==


Before enabling the CBPolicyd module you need to initialize it's database.

Install SQLite using the following command:

{{{
apt-get install sqlite3
}}}

Change directory to:

{{{
cd /opt/zimbra/cbpolicyd/share/database/
}}}

Change '''convert-tsql''' to be an executable file by:

{{{
chmod a+x convert-tsql
}}}

The initialization procedure for a SQLite Database is the following:

{{{
for i in core.tsql access_control.tsql quotas.tsql amavis.tsql checkhelo.tsql checkspf.tsql greylisting.tsql accounting.tsql do ./convert-tsql sqlite $i done > /tmp/db1.sql
}}}
 
{{{
#vi /tmp/db1.sql   --> remove all lines start with # before import db1
}}}

{{{
sqlite3 /opt/zimbra/data/cbpolicyd/db/cbpolicyd.sqlitedb < /tmp/db1.sql
}}}


== Activating the WebUI for CBPolicyd ==


Now that CBPolicyd is fully active you need a GUI to configure it, since its features are not implemented in the Zimbra Administration Console yet.
All operations must be executed as 'root' except where specified.

CBPolicyd WebUI is located in '''/opt/zimbra/cbpolicyd/share/webui/'''.
To enable it "one shot" for a quick test (this configuration will be erased if you update Zimbra) just create a symlink of the webui in the document root of Zimbra's Apache server:

{{{
cd /opt/zimbra/httpd/htdocs/ && ln -s ../../cbpolicyd/share/webui
}}}

Then, edit the '''/opt/zimbra/cbpolicyd-2.1.0-beta/share/webui/includes/config.php''' file putting a '''#''' front of all the lines beginning with '''$DB_DSN''' and adding the following line just before the line beginning with '''$DB_USER''':

{{{
$DB_DSN="sqlite:/opt/zimbra/data/cbpolicyd/db/cbpolicyd.sqlitedb";
}}}

Reload the httpd services with:

{{{
su - zimbra -c "zmapachectl restart"
}}}

You can now access the WebUI from:
{{{
http://yourzimbraserver:7780/webui/index.php
}}}

= Policyd Web Administration =


The Policyd Web Administration WebUI shows as follows:

{{attachment:WebUI.jpg}}


== Configuring CBPolicyd ==


=== Access Control ===

In the following example I need to reject relaying mails originating from NOOR Public IP address '''217.139.224.32'''. First I need to create a '''Policy Group''' to define the IP address '''217.139.224.32'''. I will name the '''Policy Group''' as '''NOOR_Public_IP '''.

1- Click '''Groups''', then choose '''Add''' from '''Action''' pulldown menu

{{attachment:PolicyGroup1.jpg}}

2- Name the '''Group''' as '''NOOR_Public_IP''' then click '''Submit Query'''

{{attachment:PolicyGroup2.jpg}}

3- Enable the '''Group''' - as it is disabled by default - by selecting it and choosing '''Change''' from '''Action''' pulldown menu

{{attachment:EnablePolicyGroup.jpg}}

4- Update '''Disabled''' from ''yes'' to '''No''', then click '''Submit Query'''

{{attachment:UpdatePolicyGroup.jpg}}

5- Now I need to define '''NOOR Public IP address'''. I will select the '''Group''' then choose '''Members''' from '''Action''' pulldown menu

{{attachment:AddPolicyGroupMember1.jpg}}

6- Then select '''Add''' from '''Action''' pulldown menu

{{attachment:AddPolicyGroupMember2.jpg}}

7- Enter '''217.139.224.32/32''' as a member of '''NOOR_Public_IP''' Policy Group, then click '''Submit Query'''

{{attachment:AddPolicyGroupMember3.jpg}}

8- Enable the '''Group Policy Member''' - as it is disabled by default - by selecting the '''Group''' and choosing '''Members''' from '''Action''' pulldown menu

{{attachment:AddPolicyGroupMember1.jpg}}

9- Then select the '''Group Policy Member''' and choose '''Change''' from '''Action''' pulldown menu

{{attachment:EnablePolicyGroupMember1.jpg}}
 
10- Update '''Disabled''' from ''yes'' to '''No''', then click '''Submit Query'''

{{attachment:EnablePolicyGroupMember2.jpg}}

'''Access Control''' deals with a '''Policy List''' which defines what is the '''Source''' and '''Destination''' '''IP address''' or '''domain''' to deal with. In my example, I will define '''NOOR Public IP address''' as the '''source''', while the '''destination''' will be ''any IP address''.

11- Click '''Main''', then choose '''Add''' from '''Action''' pulldown menu

{{attachment:AddPolicyList1.jpg}}

12- I will add a '''Test Policy''' '''List''', give it priority '''50''', and describe it as '''Test Policy''', then click '''Submit Query'''

{{attachment:AddPolicyList2.jpg}}

13- Enable the '''List''' - as it is disabled by default - by selecting it and choosing '''Change''' from '''Action''' pulldown menu

{{attachment:EnablePolicyList1.jpg}}

14- Update '''Disabled''' from ''yes'' to '''No''', then click '''Submit Query'''

{{attachment:EnablePolicyList2.jpg}}

15- Now I need to define the '''Source''' which is '''NOOR Public IP address''' and the '''Destination''' which is ''any IP address''. I will select the '''List''' then choose '''Members''' from '''Action''' pulldown menu

{{attachment:AddPolicyListMember1.jpg}}

16- Then select '''Add''' from '''Action''' pulldown menu

{{attachment:AddPolicyListMember2.jpg}}

17- Enter '''%NOOR_Public_IP''' as the '''Source''' and '''any''' as the '''Destination''', then click '''Submit Query'''

{{attachment:AddPolicyListMember3.jpg}}

18- Enable the '''Group Policy List''' - as it is disabled by default - by selecting the '''List''' and choosing '''Members''' from '''Action''' pulldown menu

{{attachment:AddPolicyListMember1.jpg}}

19- Then select the '''Policy List Member''' and choose '''Change''' from '''Action''' pulldown menu

{{attachment:EnablePolicyListMember1.jpg}}

20- Update '''Disabled''' from ''yes'' to '''No''', then click '''Submit Query'''

{{attachment:EnablePolicyListMember2.jpg}}

Now I will add the "Access Control List" that will '''reject''' relaying from '''NOOR Public IP address''' to ''any IP address''.

21- Click '''Configure''' under '''Access Control''' then select '''Add''' from '''Action''' pulldown menu

{{attachment:AddAccessControlList1.jpg}}

22- Enter '''Rejected IP address''' as the ''Name'', select '''Test Policy''' from ''Link to policy'' pulldown menu, select '''Reject''' from ''Verdict'' pulldown menu, and type '''Rejected IP address''' in ''Data'', then click '''Submit Query'''

{{attachment:AddAccessControlList2.jpg}}

23- Enable the '''Access Control List''' - as it is disabled by default - by selecting it and choosing '''Change''' from '''Action''' pulldown menu

{{attachment:EnableAccessControlList1.jpg}}

24- Update '''Disabled''' from ''yes'' to '''No''', then click '''Submit Query'''

{{attachment:EnableAccessControlList2.jpg}}

25- Now I will test my '''Reject Policy''' and I will get the following '''Non-Delivery Report'''

{{attachment:RejectedIPAddress.jpg}}


=== Quotas ===


In the following example I need to limit relaying mails originating from NOOR subnets '''217.139.0.0/16, 41.187.0.0/16 & 197.246.0.0/16''' or NOOR domain '''noor.net''' to any external domain. I will use two default '''Policy Groups''' which are '''internal_domains & internal_ips''', but first I need to check that their definitions (Members) are correct. i.e '''@noor.net''' is the member of '''internal_domains''' Policy Group and '''217.139.0.0/16, 41.187.0.0/16 & 197.246.0.0/16''' are the members of '''internal_ips''' Policy Group.

1- Click '''Groups''', then choose '''internal_domains''' Policy Group, then select '''Members''' from '''Action''' pulldown menu

{{attachment:InternalDomainsMember1.jpg}}

2- Make sure '''@noor.net''' member is defined and enabled. Otherwise add/change it to '''@noor.net'''

{{attachment:InternalDomainsMember2.jpg}}

3- Click '''Groups''', then choose '''internal_ips''' Policy Group, then select '''Members''' from '''Action''' pulldown menu

{{attachment:Internal_IP_Addresses_Members1.jpg}}

4- Make sure '''217.139.0.0/16, 41.187.0.0/16 & 197.246.0.0/16''' members are defined and enabled. Otherwise add/change them to '''217.139.0.0/16, 41.187.0.0/16 & 197.246.0.0/16'''

{{attachment:Internal_IP_Addresses_Members2.jpg}}

5- I will use the '''Default Outbound Policy list'''. Click '''Main''', then choose '''Default Outbound''' Policy List, then select '''Members''' from '''Action''' pulldown menu

{{attachment:DefaultOutbound1.jpg}}

6- '''Source = %internal_ips,%internal_domains''' means the source either belongs to Internal_IPs OR Internal_Domains Policy Group Members. '''Destination = !%internal_domains''' means the destination does not belong to Internal_Domains Policy Group Members.

{{attachment:DefaultOutbound2.jpg}}

Now I will define a '''Quota Policy''' that prevents any Internal_IPs' OR Internal_Domains' member that has an email with the format '''user@domain''' from sending more than on '''1''' message every '''60''' seconds to any external domain.
 
7- Click '''Configure''' under '''Quotas''' then select '''Add''' from '''Action''' pulldown menu 

{{attachment:AddQuotaList1.jpg}}

8- Enter '''Sending Quota''' as the ''Name'', select '''Sender:user@domain''' from ''Track'' pulldown menu, enter '''60''' as the ''Period'', select '''Default Outbound''' from ''Link to policy'' pulldown menu, select '''Reject''' from ''Verdict'' pulldown menu, and leave '''No''' in ''Stop processing here'', then click '''Submit Query'''

{{attachment:AddQuotaList2.jpg}}

9- Enable the '''Quota List''' - as it is disabled by default - by selecting it and choosing '''Change''' from '''Action''' pulldown menu

{{attachment:EnableQuotaList1.jpg}}

10- Update '''Data''' to '''Rejecting: Too many messages from sender in last 60''' and update '''Disabled''' from ''yes'' to '''No''', then click '''Submit Query'''

{{attachment:EnableQuotaList2.jpg}}

I will add a limit of 1 message to the '''Quota List'''.

11- Select the '''Quota List''' and then choose '''Limits''' from '''Action''' pulldown menu

{{attachment:AddQuotaListLimit1.jpg}}

12- Then select '''Add''' from '''Action''' pulldown menu

{{attachment:AddQuotaListLimit2.jpg}}

13- Select '''Message Count''' from ''Type'' pulldown menu, and enter '''1''' as the ''Counter Limit'', then click '''Submit Query'''

{{attachment:AddQuotaListLimit3.jpg}}

14- Enable the '''Quota List Limit''' - as it is disabled by default - by first selecting the '''Quota List''' and then choose '''Limits''' from '''Action''' pulldown menu

{{attachment:AddQuotaListLimit1.jpg}}

15- Then select the '''Quota List Limit''' and choose '''Change''' from '''Action''' pulldown menu

{{attachment:EnableQuotaListLimit1.jpg}}

16- Update '''Disabled''' from ''yes'' to '''No''', then click '''Submit Query'''

{{attachment:EnableQuotaListLimit2.jpg}}

17- Now I will test my '''Quota Limit Policy''' and I will get the following '''Non-Delivery Report'''

{{attachment:TooManyMessages.jpg}}


=== Maximum recipient limit ===

To adjust the maximum number of recipients:

{{{
su - zimbra

postconf -e 'smtpd_recipient_limit = 101'
}}}

To apply settings:

{{{
postfix reload
}}}

To check current settings:

{{{
postconf | grep smtpd_recipient_limit
}}}

= Enable Cbpolicy Authentication =

We can use web authentication and configure .htaccess to limit access into PolicyD web admin. Only authenticated user can access web admin.

    Move to the webui cbpolicyd directory

Step 1

First go to webui directy (that is symbolic linked with /opt/zimbra/httpd/htdoc/share/webui)
{{{
	# cd /opt/zimbra/cbpolicyd-2.1.0-beta/share/webui
}}}

Step 2

Create htaccess file
{{{
	# touch .htaccess
	# vi .htaccess
}}}
and add below lines

{{{
AuthUserFile /opt/zimbra/cbpolicyd-2.1.0-beta/share/webui/.htpasswd
AuthGroupFile /dev/null
AuthName "User and Password"
AuthType Basic
require valid-user
}}}

Step 3:
{{{
	touch .htpasswd
}}}
and issue below command to add user and passwod

{{{
	# apt-get install apache2-utils
	# htpasswd -c .htpasswd cbpadmin 
	or
	# htpasswd -c .htpasswd admin        (same as Zimbra admin)
}}}
	
This process will ask password for cbpadmin user.

Step 4

Edit apache config file
{{{
	# vi /opt/zimbra/conf/httpd.conf
}}}

And add below lines

{{{
Alias /webui /opt/zimbra/cbpolicyd-2.1.0-beta/share/webui/
<Directory /opt/zimbra/cbpolicyd-2.1.0-beta/share/webui>
# Comment out the following 3 lines to make web ui accessible from anywhere
AllowOverride AuthConfig
Order Deny,Allow
Allow from all
</Directory> 
}}}

And finally restart apache server from root
{{{
	 # su - zimbra -c "zmapachectl restart"
}}}
And enjoy... 
