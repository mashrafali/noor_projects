{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-11-11
Latest update: 2014-11-11
}}}
<<TableOfContents(3)>>


Let’s renew our self signed certificate for Zimbra deployment!

First of all, let’s stop all Zimbra related services (if not already done):

{{{
~]# su - zimbra

~]$ zmcontrol stop
}}}
then, create a new Certification Authority:

(Note that from now on, following commands must be run as root)

 Single-Node Self-Signed Certificate

1. Begin by generating a new Certificate Authority (CA).
{{{
 /opt/zimbra/bin/zmcertmgr createca -new
}}}

2. Then generate a certificate signed by the CA that expires in 365 days.
{{{
 /opt/zimbra/bin/zmcertmgr createcrt -new -days 365
}}}

3. Next deploy the certificate.
{{{
 /opt/zimbra/bin/zmcertmgr deploycrt self
}}}

4. Next deploy the CA.
{{{
 /opt/zimbra/bin/zmcertmgr deployca
}}}

5. To finish, verify the certificate was deployed to all the services.
{{{
 /opt/zimbra/bin/zmcertmgr viewdeployedcrt
}}}

6. Start Zimbra

{{{
~]# su - zimbra

~]$ zmcontrol start
}}}

reference:

http://wiki.zimbra.com/wiki/Administration_Console_and_CLI_Certificate_Tools
