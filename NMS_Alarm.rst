#pragma section-numbers 2
{{{
Author       : Amr Mohsen & Ayman Tohamy
Version      : 1
Creation date: 2013-4-17
Latest update: 2013-12-15
}}}
<<TableOfContents(4)>>

== How to connect to NMS ==
{{{
ssh root@10.20.10.10
}}}
== How to add alarm on NMS ==
'''1- Request template'''

{{{
Dear Team,

Kindly create a new alarm that matches the below string:

%DMVPN-5-CRYPTO_SS

With a subject of " DMVPN - Tunnel status "

Thanks,
Core Team
}}}
'''1- Backup:'''

{{{
    # cp /etc/log2mail/config/default /backup/log2mail/default.original
}}}
'''2- copy any paragraph and paste it in the same page:'''

{{{
    #vim /etc/log2mail/config/default
}}}
{{{
file = /var/log/messages
     pattern = "MGBL-SYS-5-CONFIG_I"
        mailto = syslog@noorgroup.net
        template = /etc/log2mail/IOS-XR-Configuration-report
         fromaddr = www@NMS-1
         sendtime = 1
         resendtime = 1
}}}
- change the pattern and template with alram name

{{{
file = /var/log/messages
         pattern = "%DMVPN-5-CRYPTO_SS"
        mailto = syslog@noorgroup.net
        template = /etc/log2mail/DMVPN-5-CRYPTO_SS
        fromaddr = www@NMS-1
        sendtime = 1
        resendtime = 1
}}}
- save the file and exit-->:wq

'''3- Create file under log2mail:'''

- Create file under /etc/log2mail with the same alarm name:

{{{
    #cd /etc/log2mail/
}}}
{{{
    #touch /etc/log2mail/DMVPN-5-CRYPTO_SS
}}}
{{{
    #vim /etc/log2mail/DMVPN-5-CRYPTO_SS
}}}
- change the subject as per request

{{{
From: %f
To: %t
Subject: DMVPN - Tunnel status

"%m" :

%l

Yours,
UNIX Team
}}}
'''4- Restart Service:'''

{{{
#/etc/init.d/log2mail   restart
}}}
