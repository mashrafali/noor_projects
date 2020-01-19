{{{
Author       : Ayman Tohamy
Title        : How to customize DNS zone file using Proxy Domains
Version      : 1.1
Creation date: 2016-02-04
Latest update: 2016-02-04
}}}
<<TableOfContents(4)>>

=== Overview ===

By default cPanel DNS template create the below standard DNS records and in order to fully configured DNS by other features such as allowing users to access cPanel & WHM services from standard HTTP ports, such as Port 80 and Port 443 we can enable their relative options in Tweak Settings as per below example:- 

{{{
; cPanel %cpversion%
; Zone file for %domain%
$TTL %ttl%
@      %nsttl%	IN      SOA     %nameserver%. %rpemail%. (
		%serial%	; serial, todays date+todays
		3600		; refresh, seconds
		7200		; retry, seconds
		1209600		; expire, seconds
		86400 )		; minimum, seconds

%domain%. %nsttl% IN NS %nameserver%.
%domain%. %nsttl% IN NS %nameserver2%.
%domain%. %nsttl% IN NS %nameserver3%.
%domain%. %nsttl% IN NS %nameserver4%.

%nameserverentry%. IN A %nameservera%
%nameserverentry2%. IN A %nameservera2%
%nameserverentry3%. IN A %nameservera3%
%nameserverentry4%. IN A %nameservera4%

%domain%. IN A %ip%
%domain%. IN AAAA %ipv6%

%domain%. IN MX 0 %domain%.

mail IN CNAME %domain%.
www IN CNAME %domain%.
ftp IN A %ftpip%
ftp IN AAAA %ipv6%

}}}

Example:-
 If you want to automatic create cpanel,webmail,whm ,autoconfig,autodiscover....etc

You need to allow :(Home »Server Configuration »Tweak Settings)

{{{
[1] Proxy subdomains --> on --> Redirection
[2] Proxy subdomain creation --> on --> Create DNS Records
[3] Thunderbird and Outlook autodiscover and autoconfig support --> (enables proxy subdomain and SRV record creation) --> ON --> Create SRV
[4] Enable DKIM on domains for newly created accounts --> on --> Create DKIM
[5] Enable SPF on domains for newly created accounts  --> on --> Create SPF
}}}

Below is more details about Domain settings in cPanel:-

==== Allow users to park subdomains of the server's hostname ====
This setting allows users to park subdomains on your server’s main domain.

For example, if your server's main domain name is example.com, click ''On'' to redirect user.example.com visitors to that user’s website.

This setting defaults to ''Off''.

==== Allow cPanel users to create subdomains across accounts ====
This setting allows a user to create an addon domain or subdomain of a domain that another user owns.

For example, if the user bob owns the domain example.com, the user charlie can create the store.example.com subdomain.

This setting defaults to ''Off''.

Warning:

 Do '''not''' enable this option. It can cause serious security issues.

==== Allow WHM users to create subdomains across accounts ====
This setting allows WHM users to create an addon domain or a subdomain of a domain that another user owns.

For example, if the user bob owns the domain example.com, the WHM user charlie can create an account for the store.example.com subdomain.

This setting defaults to Off.

Warning:

 Do '''not''' enable this option. It can cause serious security issues.

'''
'''

==== Allow Remote Domains ====
This setting allows users to create parked and addon domains that resolve to other servers.

This setting defaults to ''Off''.

Warning:

 Do '''not''' enable this option. It can cause serious security issues.

==== Allow resellers to create accounts with subdomains of the server's hostname ====
This setting allows resellers to create accounts with subdomains of your server’s main domain.

For example, if your main domain name is example.com, enable this setting to redirect user.example.com visitors to the reseller’s website.

This setting defaults to ''Off''.

==== Allow unregistered domains ====
This setting allows users to create domain names on the server that they do '''not''' register with a valid registrar.

This setting defaults to ''Off''.

==== Automatically add A entries for registered nameservers when creating a new zone ====
This setting specifies whether the system automatically creates [[https://documentation.cpanel.net/display/ALD/Add+an+A+Entry+for+Your+Hostname|A entries]] for a new domain's registered nameservers when a user creates a domain.

This setting defaults to ''On''.

==== Prevent cPanel users from creating specific domains ====
This setting prevents the creation of certain domains with domain names that the /var/cpanel/commondomains file contains.

If you enable this setting, cPanel users '''cannot''' create (as an addon or parked domain) any domain that the /var/cpanel/commondomains file or the /usr/local/cpanel/etc/commondomains file contains.

Important:

 Do '''not''' edit the /usr/local/cpanel/etc/commondomains file directly. If you do, the system '''will overwrite''' your changes whenever cPanel & WHM updates.

==== Check zone syntax ====
This setting allows you to have the system automatically check zone file syntax whenever a user saves or synchs DNS zone files.

This setting's value defaults to ''On''.

==== Check zone owner ====
This  setting allows you to have the system automatically check a DNS zone’s  owner whenever a user saves or synchs DNS zone files.

This setting defaults to ''On''.

==== Enable DKIM on domains for newly created accounts ====
[[http://www.dkim.org/|DKIM (DomainKeys Identified Mail)]] verifies a message's sender and integrity. It allows an email system to prove that a message is valid, '''not''' forged, and that it came from the specified domain.

This setting allows you to specify whether to enable DKIM for new accounts by default.

This setting defaults to ''On''.

{{{
To install DKIM:-

For Single Username
cPanel script to enable DKIM via commandline:

Syntax

#  /usr/local/cpanel/bin/dkim_keys_install $username

Replace $username with the actual cPanel username.
}}}

{{{
For All users
Step I : Login to server as root.

Step II : Change the directory to “/var/cpanel/users“

# cd /var/cpanel/users

Step III : Execute the following;

for i in `ls /var/cpanel/users` ;do /usr/local/cpanel/bin/dkim_keys_install $i ;done
}}}

==== Enable SPF on domains for newly created accounts ====
[[http://www.openspf.org/|SPF (Sender Policy Framework)]] denies spammers the ability to send email when they forge your domain’s name as the sender (spoofing). This authentication function adds IP addresses to a list of computers that you authorize to send mail from your domain name.  It verifies that messages that your domain sends come from the listed  sender, which reduces the amount of backscatter that you receive.

This setting allows you to specify whether to enable SPF for new accounts by default.

This setting defaults to ''Off''.

{{{
If you switch it to On and you need to update current account:
For Single Username:
cPanel script to enable SPF via commandline:

Syntax

# /usr/local/cpanel/bin/spf_installer $username

Where, $username is the cPanel user name.
}}}
{{{
For All Usernames

Step I : Login to server as root.

Step II : Change the directory to “/var/cpanel/users“

# cd /var/cpanel/users

Step III : Execute the following;

for i in `ls /var/cpanel/users` ;do /usr/local/cpanel/bin/spf_installer $i ;done

}}}

==== DNS request management application ====
This setting specifies the application that the system uses to handle DNS management requests.

To specify a new application, enter the path to the application that you wish to use in the text box.

This setting defaults to ''dnsadmin, auto-detect SSL''.

==== Proxy subdomains ====
This setting allows users to access cPanel & WHM services from standard HTTP ports, such as Port 80 and Port 443. This setting is useful if users '''cannot''' access the standard cPanel & WHM ports due to firewall restrictions.

If  you enable this setting, the system automatically redirects visitors to  the correct port when they access the following subdomains:

 * cpanel.example.com redirects to the user's cPanel [[https://documentation.cpanel.net/display/ALD/The+cPanel+Home+Interface+-+x3|cPanel Home Interface]].
 * webmail.example.com redirects to the user's ''Webmail'' interface.
 * webdisk.example.com redirects to the user's Web Disk.
 * whm.example.com redirects to the user's WHM interface.

Note:

 example.com represents the user's domain name.

This setting defaults to ''On''.

Important:

  * The settings that you select in the ''[[https://documentation.cpanel.net/display/ALD/Tweak+Settings+-+Redirection|Tweak Settings - Redirection]]'' section do '''not''' apply to proxy subdomains.
 * When you enable this setting, it creates an entry in your Apache configuration file (httpd.conf). This feature also requires that you '''do ''''''not''' manually disable mod_rewrite and mod_proxy in the httpd.conf file.

==== Proxy subdomain creation ====
Proxy  subdomains allow users to reach particular interfaces within cPanel or  WHM when they enter a subdomain in a browser. For example, a user who  enters cpanel.example.com reaches example.com's [[https://documentation.cpanel.net/display/ALD/The+cPanel+Home+Interface+-+x3|cPanel Home Interface]].

This setting allows WHM to automatically create DNS entries for the following subdomains for every user’s account:

 * cpanel.example.com
 * webmail.example.com
 * webdisk.example.com
 * whm.example.com

Note:

 example.com represents the user's domain name.

This setting defaults to ''On''.

Important:

 You '''must''' add DNS entries for these subdomains to work. Use the /usr/local/cpanel/scripts/proxydomains file to create DNS entries manually.

{{{
The best way to do this is to specify the username:
--------------------------------
/scripts/proxydomains --user=username add

cpanel [~]#                         /scripts/proxydomains --user=nooradmin add
Adding proxy subdomains for user nooradmin.
test.com                        [test.com, 41.187.100.24, +A@webdisk, +A@whm, +A@webmail, +A@cpanel, +A@cpcalendars, +A@cpcontacts]


To do all accounts on the server (which can take a while):
---------------------------------------------------------------
/scripts/proxydomains add

# /scripts/proxydomains add
Adding proxy subdomains for all users.
This may take several minutes if there are many accounts on the system.
almaza-avenue.com                  [almaza-avenue.com, 41.187.100.24, no changes needed]
babrezkgroup.com                   [babrezkgroup.com, 41.187.100.24, no changes needed]
biotechgulf.com                    [biotechgulf.com, 41.187.100.24, +A@webdisk, +A@whm, +A@cpcalendars, +A@cpcontacts]

}}}


==== Thunderbird and Outlook autodiscover and autoconfig support (enables proxy subdomain and SRV record creation) ====
This setting automatically creates autodiscover and autoconfig proxy subdomains when you create a domain.

 * autoconfig.example.com
 * autodiscover.example.com

This setting also creates the autodiscover and autoconfig SRV records that local domains need for Outlook and Thunderbird email autoconfiguation.

Note:

 If you turn off the ''Proxy subdomains'' option, the system disables this setting.

For more information about Autodiscover and AutoConfig, visit the [[http://technet.microsoft.com/en-us/library/bb124251.aspx|TechNet for Autodiscover]] and [[https://developer.mozilla.org/en-US/docs/Mozilla/Thunderbird/Autoconfiguration?redirectlocale=en-US&redirectslug=Thunderbird/Autoconfiguration|Mozilla's AutoConfig]] websites.

==== Preferred mail service to configure to use for Thunderbird and Outlook® autodiscover and autoconfig support ====
This  setting allows you to choose the email transfer method to use with  Thunderbird and Outlook with Autodiscover and AutoConfig support.

This setting defaults to ''imap''.

Notes:

  * We recommend that you select IMAP and '''not''' POP3.
 * You '''must''' enable the ''Thunderbird and Outlook autodiscover and autoconfig support (enables proxy subdomain and SRV record creation)'' option in order to configure this setting.

==== Host to publish in the SRV records for Outlook autodiscover support ====
Microsoft  Outlook®’s Autodiscover service searches DNS for an SRV record for an  email inbox’s domain that points to a particular server for  Autodiscover. By default, this server is cpanelemaildiscovery.cpanel.net.

This setting allows system administrators to perform the following actions:

 * Choose the host that the system publishes to the SRV records.
 * Change the default host if they have an SSL-enabled host with an SSL certificate that a Certificate Authority signs.
 * Use  their own server for Outlook® Autodiscover. Enter that server’s Fully  Qualified Domain Name (FQDN) in the available text box.
 * When you enable the ''Host to publish in the SRV records for Outlook autodiscover support'' feature, the system queries the server that you specify for the Autodiscover settings. You '''must''' have a custom XML file for this feature to function properly.

Notes:

  * For more information about how to use a custom XML file, visit [[https://wiki.mozilla.org/Thunderbird:Autoconfiguration|Mozilla's autoconfiguration page]], or the [[http://technet.microsoft.com/en-us/library/bb332063(EXCHG.80).aspx|Exchange's Autodiscover page]].
 * You must enable the ''Thunderbird and Outlook autodiscover and autoconfig support (enables proxy subdomain and SRV record creation)'' option in order to configure this option.

For more information about Microsoft Outlook's Autodiscover feature, visit[[http://support.microsoft.com/?kbid=940881|Microsoft's Support]] website.

==== Overwrite custom A records used for proxy subdomains ====
This setting allows the system to remove any existing custom [[https://documentation.cpanel.net/display/ALD/Add+an+A+Entry+for+Your+Hostname|A records]] that match proxy subdomains that you create or remove.

Note:

 If you turn off the ''Proxy subdomains'' option, the system disables this setting.

This setting defaults to ''Off.''

''''

==== Overwrite custom SRV records used by Outlook AutoDiscover support ====
This  setting allows the system to remove any existing custom SRV records  whenever the user adds or removes Outlook Autodiscover support.

This setting defaults to ''Off.''

''''

==== Proxy subdomain override ====
This  setting allows users to create cPanel, Webmail, Web Disk, and WHM  subdomains that override automatically generated proxy subdomains.

For example, a user can direct visitors who access cpanel.example.com to a web page that the user configures, such as mycontrolpanel.example.com.

This setting defaults to ''On''.

Note:

 Proxy  subdomains allow you to enter a subdomain in your browser to reach  particular cPanel & WHM interfaces. For example, enter cpanel.example.com to reach example.com's cPanel interface.

==== public_html subdomains only ====
This setting prevents the creation of subdomains outside of a user's own public_html directory.

This setting defaults to ''Off''.

==== Always use authoritative (registered) nameservers when creating a new DNS zone ====
This  setting allows the server to use a new domain's authoritative  nameservers (the nameservers on record with the domain's registrar). The server does '''not''' use the nameservers that domain's creator specified.

This setting defaults to ''Off''.
