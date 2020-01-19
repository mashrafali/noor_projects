## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Cpanel/Block_Domains_Exim
{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-06-17
Latest update: 2014-06-17
}}}
<<TableOfContents(3)>>

= Block Domain WHM/cPanel =
There may come a time when you’d like to block an account /  domain on your WHM/cPanel server from being able to send mail.  Here’s  how you’d get it done!

First of All Backup Exim configurations tp local server:

{{{
  From WHM - EXIM Configuration Manager- Backup]
}}}

 . We’re going to set up a ‘blocked domain’ list.  We’ll set it up once,  then have the ability to go in and add or remove domains to the text  file easily whenever we want afterwards. First, ssh into your server and add the domain name to a new file called /etc/blockeddomains

{{{
  echo "domain.com" > /etc/blockeddomains
}}}




 . (This will also create the file.  You can cat the file to make sure it worked out:

{{{
 cat /etc/blockeddomains domain.com
}}}



 . Next, set the right ownership/permissions:
{{{
 chown root.mail /etc/blockeddomains && chmod 640 /etc/blockeddomains
}}}




 . Next, load up a web browser, log into WHM and go to:

 . WHM –> Service Configuration –> EXIM Configuration Manager –> Advanced Editor Scroll down within that box about 1/4 of the way, and you’ll see something similar to: 

{{attachment:exim_editor.png|exim_editor}}

 Click on “Add additional configuration setting” and fill it out like we have it in the picture:

{{{
domainlist blocked_domains = lsearch;/etc/blockeddomains
}}}


 . Next, search for the string “ROUTERSTART”:

{{attachment:exim_editor2.png|exim_editor2}}

 Add the following into it:

{{{
reject_domains:
 
driver = redirect
domains = +blocked_domains
allow_fail
data = :fail: Connection rejected: SPAM source $domain is manually blacklisted.
}}}


 . Now you’re all set.  Next time you want to block a domain, simply add  it to the /etc/blockeddomains file.  If you want to unblock them,  remove them from that file.
