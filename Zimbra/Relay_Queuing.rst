{{{
Author       : Ayman Tohamy
Version      : 1.0
Creation date: 2013-01-15
Latest update: 2013-01-21
}}}
<<TableOfContents(3)>>

= Install Zimbra 8 =
 * install zimbra 8 mail server with below configurations

 * server hostname: mail
 * domain name : relay.noor
 * server FQDN : mail.relay.noor
 * IP address: 41.187.101.45
 * subnetmask: 255.255.255.248
 * gateway : 41.187.101.41
 * DNS : 217.139.0.38<<BR>>add DNS zone (relay.noor) at NOOR DNS server 217.138.0.38 with MX mail.relay.noor
 * how to install zimbra 8

 * <<BR>>http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Zimbra/Zimbra8_install

= Configure Relay server =
after installing server we need to let only NOOR's customers to relay through it without authentication

== Relay NOOR Subnets ==
1- allow only NOOR subnet to relay through this MTA server

From z imbra admin console - Home - Configure - Servers choose mail.relay.noor

then press Edit at the right side

for MTA - MTA trusted networks:

{{{
 217.139.0.0/16 41.187.0.0/16 197.246.0.0/16
}}}
== Disable Authentication ==
1- Disable Authentication :

for MTA - Authentication-  deactivate (disable) below options:

{{{
 Enable authentication
 TLS authentication only
}}}
== Add SMTP port 8250 ==
2- from command line Replace submission with required port (8250), also make sure that smtpd_client_restrictions=permit_mynetworks:

{{{
vi /opt/zimbra/postfix/conf/master.cf.in
}}}
{{{
smtp      inet  n       -       n       -       -       smtpd
465    inet  n       -       n       -       -       smtpd
  -o smtpd_tls_wrappermode=yes
  -o smtpd_sasl_auth_enable=yes
8250 inet n      -       n       -       -       smtpd
        -o smtpd_etrn_restrictions=reject
        -o smtpd_sasl_auth_enable=%%zimbraMtaSaslAuthEnable%%
        -o smtpd_client_restrictions=permit_mynetworks
        -o smtpd_tls_security_level=%%zimbraMtaTlsSecurityLevel%%
}}}

== Max Recipient Limit ==
to decrease the maximum number of sent messages to decrease the possibilities of sending spams, hereunder the steps to configure that:


To adjust:
{{{
su - zimbra
postconf -e 'smtpd_recipient_limit = 20'
To apply settings:
postfix reload
To check current settings:
postconf | grep smtpd_recipient_limit
}}}

= Configure Queuing server =
We need to configure secondary MX record for the domain names of NOOR customers to queue their E-mails in case of failuare or unreachability of customers' mail servers

The main target is to add domain at zimbra and make it authorized for this domain then forward all its messages again to customers' primary mail servers:

== Add domain name ==
1- Add the required domain to zimbra from GUI or Comman line:

for example: noorhosting.com

{{{
    zmprov createDomain domain
}}}
{{{
    zmprov createDomain noorhosting.com
}}}
== Make relay server responsible for domain ==
2- Run the following commands as zimbra user: Example: noorhosting.com

{{{
zmprov md domain zimbraMailCatchAllAddress @domain
zmprov md domain zimbraMailCatchAllForwardingAddress @domain
zmprov md domain zimbraMailTransport smtp:primaryMX
}}}
{{{
zmprov md noorhosting.com zimbraMailCatchAllAddress @noorhosting.com
zmprov md noorhosting.com zimbraMailCatchAllForwardingAddress @noorhosting.com
zmprov md noorhosting.com zimbraMailTransport smtp:smtp.noorhosting.com
}}}
Note: smtp.noorhosting.com is the primary MX record for the domain noorhosting.com

== Provision multiple domains ==
we have create two text files for adding domains and modifyind domain settings under /opt/zimbra/backup

1- add your domain to queueing-domains.txt file

{{{
    createDomain noorhosting.com
}}}
then Run below command:

{{{
 zimbra@mail:/root$ zmprov < /opt/zimbra/backup/queueing-domains.txt
}}}
2- to modify domain name settings, add below lines to your file queueing-domains-forward.txt :

{{{
md noorhosting.com zimbraMailCatchAllAddress @noorhosting.com
md noorhosting.com zimbraMailCatchAllForwardingAddress @noorhosting.com
md noorhosting.com zimbraMailTransport smtp:smtp.noorhosting.com
}}}
then run below command

{{{
 zimbra@mail:/root$ zmprov < /opt/zimbra/backup/queueing-domains-forward.txt
}}}
3- Restart zimbra service
{{{
prov> zimbra@mail:/root$ zmcontrol restart
Host mail.relay.noor
        Stopping vmware-ha...Done.
        Stopping zmconfigd...Done.
        Stopping stats...Done.
        Stopping mta...Done.
        Stopping spell...Done.
        Stopping snmp...Done.
        Stopping cbpolicyd...Done.
        Stopping archiving...Done.
        Stopping antivirus...Done.
        Stopping antispam...Done.
        Stopping proxy...Done.
        Stopping memcached...Done.
        Stopping mailbox...Done.
        Stopping logger...Done.
        Stopping ldap...Done.
Host mail.relay.noor
        Starting ldap...Done.
        Starting zmconfigd...Done.
        Starting logger...Done.
        Starting mailbox...Done.
        Starting antispam...Done.
        Starting antivirus...Done.
        Starting snmp...Done.
        Starting spell...Done.
        Starting mta...Done.
        Starting stats...Done.
}}}
