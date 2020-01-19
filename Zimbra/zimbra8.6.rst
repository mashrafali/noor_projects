{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 01-02-2016
}}}
<<TableOfContents(3)>>

== How to Install Zimbra 8.6 on ubuntu 14 ==
=== Install the prerequisite packages: ===
Step 1 – Update repositories.

{{{
 root@mail:/# apt-get update
 root@mail:/# apt-get upgrade
}}}
Step 2 – Install the prerequisite packages

{{{
 root@mail:/# apt-get install libgmp10 libperl5.18 libaio1 unzip pax sysstat sqlite3 perl bind9
}}}
=== Configure server: ===
Step 3 – Configure static IPs (edit as needed). Open interface config file with this command nano /etc/network/interfaces and make the following changes:

{{{
 auto eth0
iface eth0 inet static
address 192.168.0.100
netmask 255.255.255.0
network 192.168.0.0
broadcast 192.168.0.255
gateway 192.168.0.1
dns-search linux-sys-adm.com
dns-nameservers 192.168.0.100
pre-up iptables-restore < /etc/iptables/rules.v4
}}}
Step 4 – Configure host name (edit as needed). Open host config file with this command nano /etc/hosts and make the following changes:

{{{
127.0.0.1     localhost.localdomain     localhost
192.168.0.100   mail.linux-sys-adm.com       mail

and in /etc/hosname

mail.linux-sys-adm.com
}}}
Step 5 – Configure resolv.conf file with this command nano /etc/resolv.conf and make the following changes:

{{{
nameserver 192.168.0.100
search linux-sys-adm.com
Configure DNS record:
}}}
Step 6 – Configure named.conf.options file with this command nano /etc/bind/named.conf.options and make the following changes:

{{{
options {
        directory "/var/cache/bind";
        query-source address * port 53;
         forwarders {
          8.8.4.4; 8.8.8.8;
         };
        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
}}}
Step 7 – Configure named.conf.local file with this command nano /etc/bind/named.conf.local and make the following changes:

{{{
zone "linux-sys-adm.com" {
                type master;
                file "/etc/bind/db.linux-sys-adm.com";
        };

zone "1.168.192.in-addr.arpa" {
                type master;
                file "/etc/bind/db.1.168.192";
        };

zone "0.0.127.in-addr.arpa" {
                type master;
                file "/etc/bind/db.0.0.127";
        };
}}}
Step 8 – Create and configure db.linux-sys-adm.com file with this command nano /etc/bind/db.linux-sys-adm.com and make the following changes:

{{{
$TTL    604800
@       IN      SOA     mail.linux-sys-adm.com. admin.linux-sys-adm.com.        (
                                030512  ; Serial
                                604800  ; Refresh
                                86400   ; Retry
                                2419200 ; Expire
                                604800 ); Negative Cache TTL
;
@               IN      NS              mail
                IN      MX      10      mail
                IN      A               192.168.0.100
mail            IN      A               192.168.0.100
}}}
Step 9 – Create and configure db.0.0.127 file with this command nano /etc/bind/db.0.0.127 and make the following changes:

{{{
$TTL 3D
@               IN      SOA    mail.linux-sys-adm.com. admin.linux-sys-adm.com. (
                               2       ; Serial
                               8H      ; Refresh
                               2H      ; Retry
                               4W      ; Expire
                               1D)     ; Minimum TTL
                        NS     mail.linux-sys-adm.com.
1                       PTR    localhost.
}}}
Step 10 – Create and configure db.1.168.192 file with this command nano /etc/bind/db.1.168.192 and make the following changes:

{{{
$TTL 3D
@               IN      SOA    mail.linux-sys-adm.com.  admin.linux-sys-adm.com. (
                               1       ; Serial
                               8H      ; Refresh
                               2H      ; Retry
                               4W      ; Expire
                               1D)     ; Minimum TTL
                        NS     linux-sys-adm.com.
10                      PTR    linux-sys-adm.com.
}}}
Step 11 – Restart bind service:

{{{
root@mail:/# /etc/init.d/bind9 restart
}}}
Step 12 – Test dns record with this command dig linux-sys-adm.com :

{{{
dig linux-sys-adm.com

; <<gt;gt; DiG 9.9.5-3ubuntu0.3-Ubuntu <<gt;gt; linux-sys-adm.com
;; global options: +cmd
;; Got answer:
;; -gt;gt;HEADER<<- opcode: QUERY, status: NOERROR, id: 48410
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;linux-sys-adm.com.             IN      A

;; ANSWER SECTION:
linux-sys-adm.com.      604800  IN      A       192.168.0.100

;; AUTHORITY SECTION:
linux-sys-adm.com.      604800  IN      NS      mail.linux-sys-adm.com.

;; ADDITIONAL SECTION:
mail.linux-sys-adm.com. 604800  IN      A        192.168.0.100

;; Query time: 0 msec
;; SERVER:  192.168.0.100#53( 192.168.0.100)
;; WHEN: Tue Jul 21 17:21:53 EEST 2015
;; MSG SIZE  rcvd: 97
}}}
=== Install Zimbra: ===
Step 13 – Download Zimbra

{{{
wget https://files.zimbra.com/downloads/8.6.0_GA/zcs-8.6.0_GA_1153.UBUNTU14_64.20141215151116.tgz
}}}
Step 14 – Unrar Zimbra

{{{
root@mail:/# tar -xvf zcs-8.6.0_GA_1153.UBUNTU14_64.20141215151116.tgz
}}}
Step 15 – Navigate to Zimbra directory

{{{
root@mail:/# cd zcs-8.6.0_GA_1153.UBUNTU14_64.20141215151116
}}}
Step 16 – Unrar Zimbra

{{{
root@mail:/zcs-8.6.0_GA_1153.UBUNTU14_64.20141215151116/# ./install.sh
}}}
Step 17 – Install Zimbra step-by-step

{{{
Operations logged to /tmp/install.log.7384
Checking for existing installation...
    zimbra-ldap...NOT FOUND
    zimbra-logger...NOT FOUND
    zimbra-mta...NOT FOUND
    zimbra-dnscache...NOT FOUND
    zimbra-snmp...NOT FOUND
    zimbra-store...NOT FOUND
    zimbra-apache...NOT FOUND
    zimbra-spell...NOT FOUND
    zimbra-convertd...NOT FOUND
    zimbra-memcached...NOT FOUND
    zimbra-proxy...NOT FOUND
    zimbra-archiving...NOT FOUND
    zimbra-core...NOT FOUND

PLEASE READ THIS AGREEMENT CAREFULLY BEFORE USING THE SOFTWARE.
ZIMBRA, INC. ("ZIMBRA") WILL ONLY LICENSE THIS SOFTWARE TO YOU IF YOU
FIRST ACCEPT THE TERMS OF THIS AGREEMENT. BY DOWNLOADING OR INSTALLING
THE SOFTWARE, OR USING THE PRODUCT, YOU ARE CONSENTING TO BE BOUND BY
THIS AGREEMENT. IF YOU DO NOT AGREE TO ALL OF THE TERMS OF THIS
AGREEMENT, THEN DO NOT DOWNLOAD, INSTALL OR USE THE PRODUCT.

License Terms for the Zimbra Collaboration Suite:
  http://www.zimbra.com/license/zimbra-public-eula-2-5.html

Do you agree with the terms of the software license agreement? [N] y ## Type Y and Enter

Select the packages to install

Install zimbra-ldap [Y]  ## Press Enter

Install zimbra-logger [Y] ## Press Enter

Install zimbra-mta [Y] ## Press Enter

Install zimbra-dnscache [Y] ## Write N and Press Enter

Install zimbra-snmp [Y] ## Press Enter

Install zimbra-store [Y] ## Press Enter

Install zimbra-apache [Y] ## Press Enter

Install zimbra-spell [Y] ## Press Enter

Install zimbra-memcached [Y] ## Press Enter

Install zimbra-proxy [Y] ## Press Enter
Checking required space for zimbra-core
Checking space for zimbra-store
Checking required packages for zimbra-store
zimbra-store package check complete.

Installing:
    zimbra-core
    zimbra-ldap
    zimbra-logger
    zimbra-mta
    zimbra-dnscache
    zimbra-snmp
    zimbra-store
    zimbra-apache
    zimbra-spell
    zimbra-memcached
    zimbra-proxy

The system will be modified.  Continue? [N] y  ## Type Y and press Enter

Main menu

   1) Common Configuration:
   2) zimbra-ldap:                             Enabled
   3) zimbra-logger:                           Enabled
   4) zimbra-mta:                              Enabled
   5) zimbra-dnscache:                         Enabled
   6) zimbra-snmp:                             Enabled
   7) zimbra-store:                            Enabled
        +Create Admin User:                    yes
        +Admin user to create:                 admin@mail.unixmen.local
******* +Admin Password                        UNSET
        +Anti-virus quarantine user:           virus-quarantine.qhae0rny@mail.unixmen.local
        +Enable automated spam training:       yes
        +Spam training user:                   spam.bijxe4tbpz@mail.unixmen.local
        +Non-spam(Ham) training user:          ham.noduijvbx@mail.unixmen.local
        +SMTP host:                            mail.unixmen.local
        +Web server HTTP port:                 8080
        +Web server HTTPS port:                8443
        +Web server mode:                      https
        +IMAP server port:                     7143
        +IMAP server SSL port:                 7993
        +POP server port:                      7110
        +POP server SSL port:                  7995
        +Use spell check server:               yes
        +Spell server URL:                     http://mail.unixmen.local:7780/aspell.php
        +Enable version update checks:         TRUE
        +Enable version update notifications:  TRUE
        +Version update notification email:    admin@mail.unixmen.local
        +Version update source email:          admin@mail.unixmen.local
        +Install mailstore (service webapp):   yes
        +Install UI (zimbra,zimbraAdmin webapps): yes

   8) zimbra-spell:                            Enabled
   9) zimbra-proxy:                            Enabled
  10) Default Class of Service Configuration:
   s) Save config to file
   x) Expand menu
   q) Quit

Address unconfigured (**) items  (? - help)


Now, you must configure the unconfigured items. The unconfigured items will be shown with a couple of stars(****) in front of them.

As you see in the above output, the ‘admin password’ item is not configured. This unconfigured item is found under Zimbra Store module, which is number 7.

To setup the admin password, enter number 7.

Store configuration

   1) Status:                                  Enabled
   2) Create Admin User:                       yes
   3) Admin user to create:                    admin@mail.unixmen.local
** 4) Admin Password                           UNSET
   5) Anti-virus quarantine user:              virus-quarantine.qhae0rny@mail.unixmen.local
   6) Enable automated spam training:          yes
   7) Spam training user:                      spam.bijxe4tbpz@mail.unixmen.local
   8) Non-spam(Ham) training user:             ham.noduijvbx@mail.unixmen.local
   9) SMTP host:                               mail.unixmen.local
  10) Web server HTTP port:                    8080
  11) Web server HTTPS port:                   8443
  12) Web server mode:                         https
  13) IMAP server port:                        7143
  14) IMAP server SSL port:                    7993
  15) POP server port:                         7110
  16) POP server SSL port:                     7995
  17) Use spell check server:                  yes
  18) Spell server URL:                        http://mail.unixmen.local:7780/aspell.php
  19) Enable version update checks:            TRUE
  20) Enable version update notifications:     TRUE
  21) Version update notification email:       admin@mail.unixmen.local
  22) Version update source email:             admin@mail.unixmen.local
  23) Install mailstore (service webapp):      yes
  24) Install UI (zimbra,zimbraAdmin webapps): yes

Select, or 'r' for previous menu [r] 4
Set password for the admin user. Use any strong password. Press enter to update the password.

Password for admin@mail.linux-sys-adm.com (min 6 characters): [OaInFVvE] ************
Now, enter ‘r’ to return to previous menu.

Select, or 'r' for previous menu [r] r
Configuration is complete now. Press ‘a’ to apply.

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) a
Finally, type ‘yes’ to complete the setup.

Save configuration data to a file? [Yes]
Save config in file: [/opt/zimbra/config.17562]
Saving config in /opt/zimbra/config.17562...done.
The system will be modified - continue? [No] yes
Access Zimbra Admin Web console

For admin console – open up your web browser and type: https://mail.linux-sys-adm:7071
For mailbox – open up your web browser and type: http://mail.linux-sys-adm
}}}
'''18. Zimbra, is the mail server and webmail service''' we are offering to our customers, it allows several access modes :

 * HTTP only
 * HTTPS only
 * A mixed mode of HTTP and HTTPS<<BR>><<BR>>For security purposes, '''the HTTP mode should generally be avoided''', login and password informations are sent unencrypted on the network and they can easily be stolen.

Also, we only install the secure "HTTPS" mode.

To access your webmail, you will have to use this kind of address : "'''https://webmail.yourdomain.com'''".

If you use the insecure address "http://webmail.yourdomain.com" you will see an error page.

To avoid this default Zimbra's behavior, we activated the "http" -> "https" redirection, now '''when you will use the HTTP URL,''' you will '''automatically be redirected to the "HTTPS" page'''.

For people managing their own Zimbra server, the configuration is done using the console :

{{{
root@webmail:~# su – zimbra
zimbra@webmail:~$  zmtlsctl redirect
zimbra@webmail:~$  zmcontrol stop
zimbra@webmail:~$  zmcontrol start
}}}
The "zmtlsctl" command can have the following parameters : '''<<BR>>http''' – http only '''https''' – https only, http is denied. '''both''' – The user can use http:// or https:// and he will stay in the chosen mode for the rest of his connexion. '''mixed'''  – When a user connects using http://, he is redirected to "https", but  only for the log in step, then he returns to the http:// mode. When  using https://, the whole session is using https://

'''Successfully make it work by doing :'''

 1. open firewall port for 7071
 1. go to admin console
 1. go to configure->servers->your.domain.whatever->proxy
 1. change Web Proxy Mode to redirect
 1. save
