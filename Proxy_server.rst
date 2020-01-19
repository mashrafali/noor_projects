## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Proxy_server
## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Proxy server
{{{
Author       : Amr Mohsen & Ayman Tohamy
Version      : 1.1
Creation date: 2012-11-14
Latest update: 2014-12-10
}}}
<<TableOfContents(3)>>

= Install Proxy =
== connect ssh on the server ==
{{{
ssh username@IP-address
}}}
== Update Server ==
Instal the server and update it with all patches using:

{{{
yum update -y
}}}
== Install HTTP service ==
{{{
yum install httpd
}}}
= Configure proxy =
{{{
sudo vi /etc/httpd/conf/httpd.conf
}}}
== Enable Modules ==
UN-comment

{{{
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_ftp_module modules/mod_proxy_ftp.so
LoadModule proxy_http_module modules/mod_proxy_http.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
}}}
== Configure Proxy module ==
UN-Comment

{{{
<IfModule mod_proxy.c>
ProxyRequests Off
<Proxy *>
    Order deny,allow
    Allow  from all
</Proxy>
}}}
== Restart the http service ==
{{{
sudo /etc/init.d/httpd restart
}}}
== To prevent directory browsing un-comment this line ==
{{{
<Directory "/var/www/html/welcome">
Options Indexes MultiViews
AllowOverride None
Order allow, deny
Allow from all
</Directory>
}}}
== Disable sql injection ==
To disable sql injection add this lines

{{{
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteCond %{QUERY_STRING} ^.*(md5|benchmark|union|select|insert|cast|set|declare|drop|update|delete).*
RewriteRule ^(.*)$ - [F, L]
</IfModule>
}}}
== Enable Proxy Redirect ==
To redirect from one URL to another

{{{
ProxyPass / http://SITE-NAME/
}}}
= Enable SSL on Proxy =
We need to configure proxy server to use HTTPS instead of HTTP

== Install mod_ssl ==
List of installed modules

{{{
# ls -l  /etc/httpd/modules
}}}
Install mod_ssl & openssl

{{{
# yum install mod_ssl openssl
}}}
Enable Module mod_ssl at httpd.conf or ssl

{{{
# cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.bak
}}}
Edit the apache file httpd.conf

{{{
# vi /etc/httpd/conf/httpd.conf

turn on the required modules...
-----------------
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
LoadModule proxy_http_module modules/mod_proxy_http.so
------------

# vi /etc/httpd/conf.d/ssl.conf
LoadModule ssl_module modules/mod_ssl.so
------------
}}}
== Configure SSL certificate ==
=== Generate a self-signed certificate ===
Using OpenSSL we will generate a self-signed certificate. If you are using this on a production server you are probably likely to want a key from a Trusted Certificate Authority, but if you are just using this on a personal site or for testing purposes a self-signed certificate is fine. To create the key you will need to be root so you can either su to root or use sudo in front of the commands

# Generate private key

{{{
        # openssl genrsa -out ca.key 2048
}}}
# Generate CSR

{{{
        # openssl req -new -key ca.key -out ca.csr
}}}
{{{
You are about to be asked to enter information that will be incorporated
into your certificate request.

Country Name (2 letter code) [GB]:EG
State or Province Name (full name) [Berkshire]:Cairo
Locality Name (eg, city) [Newbury]:Cairo
Organization Name (eg, company) [My Company Ltd]:NOOR
Organizational Unit Name (eg, section) []:NOOR
Common Name (eg, your name or your server's hostname) []:reseller.noor.com
Email Address []:info@noor.net

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:NOOR
}}}
# Generate Self Signed Key

{{{
# 1 year
        openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
# 7 years
        openssl x509 -req -days 2555 -in ca.csr -signkey ca.key -out ca.crt
}}}
=== Copy Certificate files ===
# Copy the files to the correct locations

{{{
        cp ca.crt /etc/pki/tls/certs
        cp ca.key /etc/pki/tls/private/ca.key
        cp ca.csr /etc/pki/tls/private/ca.csr
}}}
WARNING: Make sure that you copy the files and do not move them if you use SELinux. Apache will complain about missing certificate files otherwise, as it cannot read them because the certificate files do not have the right SELinux context.

If you have moved the files and not copied them, you can use the following command to correct the SELinux contexts on those files, as the correct context definitions for /etc/pki/* come with the bundled SELinux policy.

{{{
        restorecon -RvF /etc/pki
}}}
=== Update the Apache SSL configuration file ===
# Then we need to update the Apache SSL configuration file

Change the paths to match where the Key file is stored. If you've used the method above it will be

{{{
# vi /etc/httpd/conf.d/ssl.conf

#SSLCertificateFile /etc/pki/tls/certs/localhost.crt
SSLCertificateFile /etc/pki/tls/certs/ca.crt

#SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
SSLCertificateKeyFile /etc/pki/tls/private/ca.key
}}}
Quit and save the file and then restart Apache

=== Restart Apache Service ===
{{{
/etc/init.d/httpd restart
}}}
== Enable ProxyPass to SSL ==
=== Edit SSL file configurations ===
{{{
# vi /etc/httpd/conf.d/ssl.conf

SSLProxyEngine on
#ProxyPass / website
#ProxyPassReverse / website

ProxyPass / http://polaris.noor.net/
ProxyPassReverse / http://polaris.noor.net/
}}}
Quit and save the file and then restart Apache

=== Restart Apache Service ===
{{{
# /etc/init.d/httpd restart
}}}
== Rewrite HTTP to HTTPS ==
To redirect for example http://reseller.noor.com to https://reseller.noor.com

=== Create a RewriteRule ===
In the main apache config, we’ll create a RewriteRule so that all http requests are re-written to https requests:

{{{
# vi /etc/httpd/conf/httpd.conf

-------------------------------
</IfModule>

<IfModule mod_rewrite.c>
RewriteEngine On
# SQL Injection
RewriteCond %{QUERY_STRING} ^.*(md5|benchmark|union|select|insert|cast|set|declare|drop|update|delete).*
#RewriteRule ^(.*)$ - [F, L]
RewriteRule ^.*$ - [F,L]

# Add this to redirect HTTP website to HTTPS
RewriteCond %{HTTPS} off
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
</IfModule>
}}}
Quit and save the file and then restart Apache

=== Restart Apache Service ===
{{{
# /etc/init.d/httpd restart
}}}


= Proxy Hosting Multiple Domain Names =


{{{
# vi /etc/httpd/conf/httpd.conf
}}}

{{{
###### NOOR.NET Website ###############
# Put this after the other LoadModule directives
LoadModule proxy_module /usr/lib/apache2/modules/mod_proxy.so
LoadModule proxy_http_module /usr/lib/apache2/modules/mod_proxy_http.so
 
# Put this with your other VirtualHosts, or at the bottom of the file
NameVirtualHost *

<VirtualHost *:80>
    DocumentRoot /var/www/html/web1
    ServerName noor.net
    ServerAlias www.noor.net

    ProxyRequests Off
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    ProxyPass / http://www.noor.net/
    ProxyPassReverse / http://www.noor.net/
    <Location />
        Order allow,deny
        Allow from all
    </Location>
</VirtualHost>

############ Zabatnee Website ###################

<VirtualHost *:80>
    DocumentRoot /var/www/html/web2
    ServerName zabatnee.com
    ServerAlias test.zabatnee.com

    ProxyRequests Off
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    ProxyPass / http://nacelle.zabatnee.com/
    ProxyPassReverse / http://nacelle.zabatnee.com/
    <Location />
        Order allow,deny
        Allow from all
    </Location>
</VirtualHost>



###########################################################
# Comment Global proxy settings
## ProxyPass / http://www.noor.net/
}}}

= Configure Firewall =
Configuring the firewall

You should now have a site working over https using a self-signed certificate. If you can't connect you may need to open the port on your firewall. To do this amend your iptables rules:

{{{
# To enable iptables
chkconfig iptables on

# Allow Port 80, 443 form all
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Allow SSH from NOOR Premises
iptables -A INPUT -s 217.139.224.0/24 -p tcp --dport 22 -j ACCEPT


#Accept incoming traffic corresponding to established outgoing sessions. 

iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

or

 iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
 iptables -I INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Optionally allow all outbound
iptables -I OUTPUT -o eth0 -d 0.0.0.0/0 -j ACCEPT

# Assign a rule to drop any other incoming connections. 

# iptables -A INPUT -j DROP

}}}
{{{

# Save Iptables


# service iptables start
/sbin/service iptables save
iptables -L -v

}}}

{{{

# iptables -L -v
Chain INPUT (policy ACCEPT 6 packets, 405 bytes)
 pkts bytes target     prot opt in     out     source               destination         
15473 5068K ACCEPT     tcp  --  any    any     anywhere             anywhere            tcp dpt:https 
  118 13957 ACCEPT     tcp  --  any    any     anywhere             anywhere            tcp dpt:http 
  590 45000 ACCEPT     tcp  --  any    any     217.139.224.0/24     anywhere            tcp dpt:ssh 
  912 1012K ACCEPT     all  --  any    any     anywhere             anywhere            state RELATED,ESTABLISHED 
    0     0 DROP       all  --  any    any     anywhere             anywhere            



}}}

{{{
-------------
Allow PING:
--------------
[proxy ~]# iptables -D INPUT -j DROP   
[proxy ~]# iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
[proxy ~]# iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT
[proxy ~]# iptables -A INPUT -j DROP 
[proxy ~]# /sbin/service iptables save
Saving firewall rules to /etc/sysconfig/iptables:          [  OK  ]

iptables -L -v
---
}}}

= Apache Running at startup =


Check Configurations

{{{
# chkconfig --list
httpd           0:off   1:off   2:off   3:off   4:off   5:off   6:off
iptables        0:off   1:off   2:on    3:on    4:on    5:on    6:off

}}}

Add HTTPD to level 345, so after restart service started automatically

{{{
chkconfig --level 345 httpd on

[root@proxy ~]# chkconfig --level 345 httpd on

[root@proxy ~]# chkconfig --list | grep httpd
httpd           0:off   1:off   2:off   3:on    4:on    5:on    6:off
}}}

= References =
http://wiki.centos.org/HowTos/Https

[[http://stackoverflow.com/questions/16130303/apache-config-how-to-proxypass-http-requests-to-https<BR>|http://stackoverflow.com/questions/16130303/apache-config-how-to-proxypass-http-requests-to-https]]

http://blog.zwiegnet.com/linux-server/configure-apache-for-ssl-https-centos-6/
