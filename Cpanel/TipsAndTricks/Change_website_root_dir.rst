## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Cpanel/TipsAndTricks/Changeroot
{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Changing website root directory.
Creation date: 2016-07-18
Latest update: 2016-07-18
}}}
There are two types of domains on a cPanel box that can have document roots: 

Main (primary) domains 

and Addon domains. 

== Changing the document root for Addon domains ==

Simply log into your cPanel and navigate to: Domains >> Addon domains.

Next, edit the Addon domain path. To do so, simply click the edit icon next to the path, and type in your new path.

== Changing Primary domain root directory ==

For changing the main/primary domain, you will need to have root SSH access and be able to locate and edit the following file (replacing your user & domain info):
{{{
/var/cpanel/userdata/USERNAME/DOMAIN.COM
}}}

{{{
vim /var/cpanel/userdata/nooradmin/noorhosting.net
}}}

1. Once you have opened the file, look for the following line:

{{{
documentroot: /home/USERNAME/public_html
}}}

{{{
documentroot: /home/nooradmin/public_html/noorhosting.net
}}}

2. Modify the location according to your needs. Save it and exit.

3. Rebuild the Apache conf and restart Apache:
{{{
/scripts/rebuildhttpdconf
service httpd restart
}}}

The change will be immediate. Simply clear your browser cache and force refresh the page
