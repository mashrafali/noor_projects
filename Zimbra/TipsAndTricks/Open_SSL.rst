{{{
Author        : Ahmed AbdelSattaar 
Documented by : Ayman Tohamy
Version       : 1.1
Creation date : 2014-05-05
}}}

== Update server distribution ==
(as root)
{{{
apt-get update
apt-get dist-upgrade
reboot
}}}

== Install Zimbra openssl Patch ==

{{{
(as root)
1) wget http://files.zimbra.com/downloads/security/zmopenssl-updater.sh
2) chmod a+rx zmopenssl-updater.sh
3) ./zmopenssl-updater.sh
}}}

{{{
 [Generates the following output]
 Downloading patched openssl
 Validating patched openssl: success
 Backing up old openssl: complete
 Installing patched openssl: complete
 OpenSSL patch process complete.
 Please restart Zimbra Collaboration Suite as the Zimbra user via zmcontrol  restart
}}}


== Restart Zimbra services ==
{{{
(as user zimbra)
4) su - zimbra
5) zmcontrol restart

}}}

== Re-Generate Server Certificate ==

{{{
Click install  Certificates -> select certificate (i.e. logs.noor.net) -> Select Install the self-siged certificate 

check Replace the existing CSR -> Click install -> Click install
}}}


== Reboot server ==
{{{

(as root)
reboot
}}}
