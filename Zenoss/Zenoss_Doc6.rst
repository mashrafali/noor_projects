{{{
Author       : Mohamed Ashraf
Title        : Zenoss Documentation SSL & Caching : Securing Web Connections
Version      : 1.0
Creation date: 21/10/2014
Latest update: 21/10/2014
}}}


=== The following steps has been applied in order to install APACHE as the front facing web server instead of ZENOSS ZOPE web server, and enable TLS encryption & mem/disk caching capabilities: ===


'''1 - Access server as root:'''

{{{
#su          -> if u have root password
}}}

or

{{{
#sudo su    -> if u have sudo password
}}}

'''2 - Create the following Script and name it "APACHE-FOR-ZENOSS.sh" :'''

{{{

#! /bin/bash

############# RUN AS ROOT

##################### TO ENABLE SSL and MEM/DISK CACHING FOR ZENOSS ZOPE [By Mohamed Ashraf]



# ENABLING APACHE MODULES
cd ~
a2enmod ssl
a2enmod rewrite
a2enmod ssl
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_ajp
a2enmod rewrite
a2enmod deflate
a2enmod headers
a2enmod proxy_balancer
a2enmod proxy_connect
a2enmod proxy_html
a2enmod rewrite
a2enmod ssl
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_ajp
a2enmod rewrite
a2enmod deflate
a2enmod headers
a2enmod proxy_balancer
a2enmod proxy_connect
a2enmod proxy_html
a2enmod cgi


# CONFIGUIRING DEFAULT SITE DIRECTIVE
echo '
<VirtualHost *:80>
        RewriteEngine on
        RewriteCond %{SERVER_PORT} !^443$
        RewriteRule ^/(.*) https://%{HTTP_HOST}/$1 [NC,R=301,L]
</VirtualHost>


<VirtualHost _default_:443>

        RewriteEngine On
        RewriteRule ^/(.*) http://localhost:8080/VirtualHostBase/https/zenoss-sys.noor.net:443/VirtualHostRoot/$1 [L,P]

        SSLEngine On
        SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
        SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
</VirtualHost>
' > /etc/apache2/sites-available/default

# CONFIGUIRING MEM_CACHE

echo '
<IfModule mod_mem_cache.c>
        CacheEnable mem /
        MCacheSize 134217728
        MCacheMaxObjectCount 100
        MCacheMinObjectSize 1
        MCacheMaxObjectSize 4194304 
</IfModule>
' >/etc/apache2/mods-available/mem_cache.conf

a2enmod mem_cache

# CONFIGURING DISK_CACHE

echo '
<IfModule mod_disk_cache.c>
# cache cleaning is done by htcacheclean, which can be configured in
# /etc/default/apache2
#
# For further information, see the comments in that file,
# /usr/share/doc/apache2.2-common/README.Debian, and the htcacheclean(8)
# man page.

        # This path must be the same as the one in /etc/default/apache2
        CacheRoot /var/cache/apache2/mod_disk_cache

        # This will also cache local documents. It usually makes more sense to
        # put this into the configuration for just one virtual host.

        CacheEnable disk /

        CacheDirLevels 5
        CacheDirLength 3
</IfModule>
' /etc/apache2/mods-available/disk_cache.conf

a2enmod disk_cache


# CONFIGUIRING CACHE-LOCK

echo '
#ENABLE CACHE-LOCK
<IfModule mod_cache.c>
CacheLock on
CacheLockPath /tmp/mod_cache-lock
CacheLockMaxAge 5
</IfModule>
' >> /etc/apache2/apache2.conf


/etc/init.d/apache2 restart


## STARTING ZENOSS SIDE CONFIGURTION

OLDSITE="src string:http://maps.googleapis.com/maps/api/js?key=${apikey}&sensor=false;"
NEWSITE="src string:https://maps-api-ssl.google.com/maps/api/js?key=${apikey}&sensor=false;"

# BACKING UP LOCATIONGEO
cd /usr/local/zenoss/Products/ZenWidgets/skins/zenui
cp locationGeoMap.pt locationGeoMap.pt.BACKUP
cp simpleLocationGeoMap.pt simpleLocationGeoMap.pt.BACKUP


# ADDING MAP TYPE FEATURE
cd /usr/local/zenoss/Products/ZenWidgets/skins/zenui/javascript/
cp geomap.js geomap.js.BACKUP

sed -i 's/mapTypeControl: false/mapTypeControl: true/g' geomap.js

echo " Done HERE "
sleep 1
cd ~
}}}

- Make the script executable then run it (as root)

{{{
#chmod a+x APACHE-FOR-ZENOSS.sh
./APACHE-FOR-ZENOSS.sh
}}}

- This Script will do all the work for you.


'''3- Replace the following:'''

- The Script has already backed up all files that you will need to edit for you, so u don't have to worry about this.

- IN /usr/local/zenoss/Products/ZenWidgets/skins/zenui/locationGeoMap.pt

REPLACE:

{{{
src string:http://maps.googleapis.com/maps/api/js?key=${apikey}&sensor=false;
}}}

WITH:

{{{
src string:https://maps-api-ssl.google.com/maps/api/js?key=${apikey}&sensor=false;
}}}

- IN /usr/local/zenoss/Products/ZenWidgets/skins/zenui/simpleLocationGeoMap.pt


REPLACE:

{{{
src string:http://maps.googleapis.com/maps/api/js?key=${apikey}&sensor=false;
}}}

WITH:

{{{
src string:https://maps-api-ssl.google.com/maps/api/js?key=${apikey}&sensor=false;
}}}


'''4- Block Port 8080 to be accessed via UFW'''

- apply the following command

{{{
#ufw status numbered
}}}

- Then find the rule for port 8080

EX:
{{{
[ 1] 8080                       ALLOW IN    217.139.224.0/24
}}}

- Delete the rule number so UFW will block this port access directly to Zenoss ZOPE (Zenoss web serve)

{{{
# ufw delete 1
}}}


- If UFW is not installed as in Zenoss-systems, you can apply the following commands for iptables, to block all NON-LOCAL 8080 connection

{{{
iptables -A INPUT -p tcp --dport 8080 -s localhost -j ACCEPT
iptables -A INPUT -p tcp --dport 8080 -j DROP
}}}
'''5- (Optional) Reboot:'''

- Reboot server to start fresh Apache disk/mem cache and refresh Zope cache (Which will work as a 2nd layer cache)
