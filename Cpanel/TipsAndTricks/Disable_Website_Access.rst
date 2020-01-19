{{{
Author       : Ayman Tohamy
Title        : How to Access to cPanel website
Version      : 1.1
Creation date: 2016-02-08
Latest update: 2016-02-08
}}}
<<TableOfContents(4)>>

== Overview ==
If we need to disable access to one cPanel websites, the simplest way to do that is to add htaccess file and grany deny access for all except NOOR  and customer gateway IP address till the customer fixing his/her website issue.



== htaccess ==
1- create .htaccess file and past the following code.

single ip address access 

{{{
order deny,allow 
deny from all 
allow from 192.168.1.1 
}}}

multiple ip address access

{{{
order deny,allow
deny from all
#allow from NOOR
allow from 217.139.224.32
allow from 217.139.224.33
allow from 217.139.224.34

#allow from Customer
allow from 197.246.35.196
}}}
Authorize the ip for your client only he will be able to access site without any problem no one else.

2- Test Access website http://domainname

{{{

You will receive below page:-

Forbidden

You don't have permission to access / on this server.

Additionally, a 403 Forbidden error was encountered while trying to use an ErrorDocument to handle the request.
}}}


== Other Options ==
Please note there are other methods such as changing website directory permissions (to be granted for only root) or to redirect it to another suspended page.

{{{
1. chmod 000 public_html
2. chown root:root public_html
}}}

That will prevent files under public_html from being served, and prevent them from enabling it again.
