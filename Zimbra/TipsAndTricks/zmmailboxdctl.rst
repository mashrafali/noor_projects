## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Zimbra/Tips_tricks/zmmailboxdctl
## page was renamed from Enterprise/IT/Docs/How_To/Linux/Zimbra/Zimbra_tips_tricks/zmmailboxdctl
{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2012-12-12
Latest update: 2012-12-12
}}}
<<TableOfContents(3)>>

=== zmmailboxdctl is not running ===
Can be indicated by

{{{
[zimbra@example.com ~]$ zmcontrol start
Host example.com
        Starting logger...FAILED


        Starting mailbox...Done.
        Starting antispam...Done.
        Starting antivirus...Done.
        Starting spell...Done.
        Starting mta...Done.
        Starting stats...Done.
}}}
or

{{{
[zimbra@example.com ~]$ zmcontrol status
Host example.com
        antispam                Running
        antivirus               Running
        ldap                    Running
        logger                  Running
        mailbox                 Stopped
                zmmailboxdctl is not running
        mta                     Running
        spell                   Running
        stats                   Running
}}}
Your http (Apache) server is running and port 80 is in use. Stop http server and now start zimbra.

{{{
zimbra:~# /etc/init.d/apache2 stop
 * Stopping web server apache2
 ... waiting .   ...done.
}}}

{{{
zimbra:~# /etc/init.d/zimbra start
Host smtp.noor.net
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

{{{
zimbra:~# su zimbra
zimbra:/root$ zmcontrol status
Host smtp.noor.net
        antispam                Running
        antivirus               Running
        ldap                    Running
        logger                  Running
        mailbox                 Running
        mta                     Running
        snmp                    Running
        spell                   Running
        stats                   Running
        zmconfigd               Running
}}}
