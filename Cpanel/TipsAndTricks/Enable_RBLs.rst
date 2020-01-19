{{{
Author       : Ayman Tohamy
Version      : 1.1
Title: Enable Cpanel RBLs
Creation date: 2015-01-29
Latest update: 2015-01-29
}}}
<<TableOfContents(3)>>


'''''For cPanel & WHM 11.48'''''

(''Home >> Service Configuration >> Exim Configuration Editor >> RBLs'')

== Overview ==
You can use the ''RBLs'' interface  to configure your mail server to check incoming mail against the  available RBLs. Your server will block the incoming messages if the IP  address or hostname matches an RBL entry.

RBL is short for “Real-time Blackhole List.” RBL servers keep lists of spam-heavy IP addresses and hostnames so that you can easily block them. The WHM interface accesses two RBLs: [[http://bl.spamcop.net/|bl.spamcop.net]] and [[http://www.spamhaus.org/zen|zen.spamhaus.org]].

== Select RBL options ==
To select RBLs options, perform the following steps:

 1. Click the ''RBLs'' tab.
 1. For each option, select the desired setting, or enter the requested information in the appropriate text box.
 1. Click ''Save''.

=== Add a custom RBL ===
To add a custom RBL, perform the following steps:

 1. Click the ''RBLs'' tab.
 1. Click ''Manage custom RBLs''. You will be directed to a new page.
 1. Enter an RBL name, RBL info URL, and DNS list in the appropriate text boxes.
 1. Click ''Add''.
 1. Use the configuration editor options to enable and configure the RBL, as described in the [[https://documentation.cpanel.net/display/ALD/RBLs#RBLs-SelectRBLoptions|Select RBL options]] section above.
  . Note:
   . Choose an RBL name that will remind you of the DNS list for this RBL.

=== Delete a custom RBL ===
To delete a custom RBL, perform the following steps:

 1. Click the ''RBLs'' tab.
 1. Click ''Manage custom RBLs''. You will be directed to a new page.
 1. Click ''Delete'' next to the appropriate RBL in the ''Current RBLs'' table.

=== RBL: bl.spamcop.net ===
This option allows you to reject mail at SMTP-time if the sender's host is in the bl.spamcop.net RBL. For more information, visit the [[http://bl.spamcop.net/|bl.spamcop.net]] website.

=== RBL: zen.spamhaus.org ===
This option allows you to reject mail at SMTP-time if the sender's host is in the zen.spamhaus.org RBL. For more information, visit the [[http://www.spamhaus.org/zen|zen.spamhaus.org]] website.

=== Whitelist: IP addresses that should not be checked against RBLs ===
This option allows you to choose a list of IP addresses to whitelist. These addresses will not be RBL-checked.

 . Remember:
  . Icon                 Enter one IP address per line in the text box.

=== Check Logs ===

{{{
tail -f /var/log/exim_mainlog
tail -f /var/log/exim_rejectlog
cat /var/log/exim_rejectlog | grep RBL
}}}

== Skip RBLs on specific domains ==
To skip RBLs for specific domains, log in as the root user and use your preferred text editor to create and edit the /etc/skiprbldomains file. Add the domains to the file, with one domain name per line.

After you create the /etc/skiprbldomains file, enable the skip_rbl_domains function through the ''Advanced Editor'' tab of the'' [[https://documentation.cpanel.net/display/ALD/Exim+Configuration+Manager|Exim Configuration Manager]] ''interface.

 1. Click the ''Add'' ''Additional Configuration Settings'' button.
 1. Select the menu and enter domainlist skip_rbl_domains
 1. In the text box, enter lsearch;/etc/skiprbldomains
 1. Click the ''Save'' button at the bottom of the interface.
