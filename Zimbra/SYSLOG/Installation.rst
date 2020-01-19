Describe Enterprise/IT/Docs/HowTo/Linux/Zimbra/SYSLOG/Installation here.


{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 2014-11-05
}}}
<<TableOfContents(3)>>

== Instal the server and update it ==
Instal the server and update it with all patches using:

{{{
apt-get update
apt-get upgrade
}}}
== Edit Host file ==
To edit the host file:

A- We should edit the local host file for Zimbra server by adding new record with Server's IP Adddress to point it to FQDN (Fully Qualified Domain Name) and Computer Name as below:

'''Server IP Address:''' 192.168.0.63

'''FQDN''' : logs.noor.net

'''Computer Name''': logs

B- We should also edit the Loopback with below syntax.

{{{
vi /etc/hosts
}}}
{{{
127.0.0.1       localhost.localdomain localhost
127.0.0.1       localhost.localdomain localhost
##127.0.1.1     logs
192.168.0.63    logs.noor.net   logs
# The following lines are desirable for IPv6 capable hosts
#::1     ip6-localhost ip6-loopback localhost
#fe00::0 ip6-localnet
#ff00::0 ip6-mcastprefix
#ff02::1 ip6-allnodes
#ff02::2 ip6-allrouters
}}}
C- To verify the created hosted name:

{{{
hostname -f
}}}
=== Install prerequisites packages ===
{{{
apt-get install libgmp3c2 libexpat1 libperl5.14 sysstat sqlite3
}}}
=== Dowload the Zimbra 8 Package ===
{{{
wget http://files2.zimbra.com/downloads/8.0.6_GA/zcs-8.0.6_GA_5434.UBUNTU12_64.20120907144631.tgz
}}}
=== Extract the zipped file ===
{{{
tar xzvf zcs-8.0.6_GA_5434.UBUNTU12_64.20120907144631.tgz
}}}
=== Enter Zimbra Extracted directory then run setup file ===
{{{
cd zcs-8.0.6_GA_5434.UBUNTU12_64.20120907144631
./install.sh
}}}
{{{
Checking for prerequisites...
     FOUND: NPTL
     FOUND: netcat-openbsd-1.89-3ubuntu2
     FOUND: sudo-1.7.2p1-1ubuntu5.4
     FOUND: libidn11-1.15-2
     FOUND: libpcre3-7.8-3build1
     FOUND: libgmp3c2-2:4.3.2+dfsg-1ubuntu1
     FOUND: libexpat1-2.0.1-7ubuntu1
     FOUND: libstdc++6-4.4.3-4ubuntu5.1
     FOUND: libperl5.10-5.10.1-8ubuntu2.1
Checking for suggested prerequisites...
     FOUND: perl-5.10.1
     FOUND: sysstat
     FOUND: sqlite3
Prerequisite check complete.
Checking for installable packages
Found zimbra-core
Found zimbra-ldap
Found zimbra-logger
Found zimbra-mta
Found zimbra-snmp
Found zimbra-store
Found zimbra-apache
Found zimbra-spell
Found zimbra-memcached
Found zimbra-proxy
Select the packages to install
Install zimbra-ldap [Y]
Install zimbra-logger [Y]
Install zimbra-mta [Y]
Install zimbra-snmp [Y]
Install zimbra-store [Y]
Install zimbra-apache [Y]
Install zimbra-spell [Y]
Install zimbra-memcached [N]
Install zimbra-proxy [N]
Checking required space for zimbra-core
checking space for zimbra-store
Installing:
    zimbra-core
    zimbra-ldap
    zimbra-logger
    zimbra-mta
    zimbra-snmp
    zimbra-store
    zimbra-apache
    zimbra-spell
The system will be modified.  Continue? [N]
#################################################################
#################################################################
The system will be modified.  Continue? [N] y
Removing /opt/zimbra
Removing zimbra crontab entry...done.
Cleaning up zimbra init scripts...done.
Cleaning up /etc/ld.so.conf...done.
Cleaning up /etc/security/limits.conf...done.
Finished removing Zimbra Collaboration Suite.
Installing packages
    zimbra-core......zimbra-core_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
    zimbra-ldap......zimbra-ldap_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
    zimbra-logger......zimbra-logger_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
    zimbra-mta......zimbra-mta_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
    zimbra-snmp......zimbra-snmp_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
    zimbra-store......zimbra-store_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
    zimbra-apache......zimbra-apache_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
    zimbra-spell......zimbra-spell_7.2.0_GA_2669.UBUNTU10_64_amd64.deb...done
Operations logged to /tmp/zmsetup.07042012-152442.log
Installing LDAP configuration database...done.
Setting defaults...
}}}
== Configure Zimbra settings ==
To configure Zimbra ...From Main Menu, you will find all options and by selecting the option number you can edit it. '''__<<BR>><<BR>>Note:__'''

********** + Admin Password : you need to set this option

{{{
Main menu
   1) Common Configuration:
   2) zimbra-ldap:                             Enabled
   3) zimbra-store:                            Enabled
        +Create Admin User:                    yes
        +Admin user to create:                 admin@smtp.noor.net
******* +Admin Password                        UNSET
        +Anti-virus quarantine user:           virus-quarantine.jmlf51sg@smtp.noor.net
        +Enable automated spam training:       yes
        +Spam training user:                   spam.j04jd65nb1@smtp.noor.net
        +Non-spam(Ham) training user:          ham.yiqxe9n4@smtp.noor.net
        +SMTP host:                            smtp.noor.net
        +Web server HTTP port:                 80
        +Web server HTTPS port:                443
        +Web server mode:                      http
        +IMAP server port:                     143
        +IMAP server SSL port:                 993
        +POP server port:                      110
        +POP server SSL port:                  995
        +Use spell check server:               yes
        +Spell server URL:                     http://smtp.noor.net:7780/aspell.php
        +Configure for use with mail proxy:    FALSE
        +Configure for use with web proxy:     FALSE
        +Enable version update checks:         TRUE
        +Enable version update notifications:  TRUE
        +Version update notification email:    admin@smtp.noor.net
        +Version update source email:          admin@smtp.noor.net
   4) zimbra-mta:                              Enabled
   5) zimbra-snmp:                             Enabled
   6) zimbra-logger:                           Enabled
   7) zimbra-spell:                            Enabled
   8) Default Class of Service Configuration:
   r) Start servers after configuration        yes
   s) Save config to file
   x) Expand menu
   q) Quit
Address unconfigured (**) items  (? - help)
}}}
=== To create admin user and set its password ===
A- Choose option 3 to create admin user :

{{{
Address unconfigured (**) items  (? - help) 3
Store configuration
   1) Status:                                  Enabled
   2) Create Admin User:                       yes
   3) Admin user to create:                    admin@smtp.noor.net
** 4) Admin Password                           UNSET
   5) Anti-virus quarantine user:              virus-quarantine.jmlf51sg@smtp.noor.net
   6) Enable automated spam training:          yes
   7) Spam training user:                      spam.j04jd65nb1@smtp.noor.net
   8) Non-spam(Ham) training user:             ham.yiqxe9n4@smtp.noor.net
   9) SMTP host:                               smtp.noor.net
  10) Web server HTTP port:                    80
  11) Web server HTTPS port:                   443
  12) Web server mode:                         http
  13) IMAP server port:                        143
  14) IMAP server SSL port:                    993
  15) POP server port:                         110
  16) POP server SSL port:                     995
  17) Use spell check server:                  yes
  18) Spell server URL:                        http://smtp.noor.net:7780/aspell.php
  19) Configure for use with mail proxy:       FALSE
  20) Configure for use with web proxy:        FALSE
  21) Enable version update checks:            TRUE
  22) Enable version update notifications:     TRUE
  23) Version update notification email:       admin@smtp.noor.net
  24) Version update source email:             admin@smtp.noor.net
Select, or 'r' for previous menu [r]
}}}
B- Choose option 4 to set its password

{{{
Select, or 'r' for previous menu [r] 4
Password for admin@smtp.noor.net (min 6 characters): [89j8dqkK5] password
Store configuration
   1) Status:                                  Enabled
   2) Create Admin User:                       yes
   3) Admin user to create:                    admin@smtp.noor.net
   4) Admin Password                           set
   5) Anti-virus quarantine user:              virus-quarantine.jmlf51sg@smtp.noor.net
   6) Enable automated spam training:          yes
   7) Spam training user:                      spam.j04jd65nb1@smtp.noor.net
   8) Non-spam(Ham) training user:             ham.yiqxe9n4@smtp.noor.net
   9) SMTP host:                               smtp.noor.net
  10) Web server HTTP port:                    80
  11) Web server HTTPS port:                   443
  12) Web server mode:                         http
  13) IMAP server port:                        143
  14) IMAP server SSL port:                    993
  15) POP server port:                         110
  16) POP server SSL port:                     995
  17) Use spell check server:                  yes
  18) Spell server URL:                        http://smtp.noor.net:7780/aspell.php
  19) Configure for use with mail proxy:       FALSE
  20) Configure for use with web proxy:        FALSE
  21) Enable version update checks:            TRUE
  22) Enable version update notifications:     TRUE
  23) Version update notification email:       admin@smtp.noor.net
  24) Version update source email:             admin@smtp.noor.net
Select, or 'r' for previous menu [r]
}}}
=== To return Back to Main Menu ===
{{{
Select, or 'r' for previous menu [r] r
Main menu
   1) Common Configuration:
   2) zimbra-ldap:                             Enabled
   3) zimbra-store:                            Enabled
   4) zimbra-mta:                              Enabled
   5) zimbra-snmp:                             Enabled
   6) zimbra-logger:                           Enabled
   7) zimbra-spell:                            Enabled
   8) Default Class of Service Configuration:
   r) Start servers after configuration        yes
   s) Save config to file
   x) Expand menu
   q) Quit
}}}
=== Save Configurations ===
{{{
*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) --> press (a)
}}}
=== How to access the web page ===
{{{
https://IP-address:7071/zimbraAdmin/
}}}
