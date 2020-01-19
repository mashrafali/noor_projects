{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-02-03
Latest update: 2013-02-03
}}}
<<TableOfContents(3)>>

To disable Antivirus running on Zimbra

= From GUI =
== Disable Antivirus service ==
From '''Zimbra admin GUI''' - '''servers''' - choose '''server''' (mail.relay.noor) - '''services''' - '''Antivirus''' --> Uncheck

== Disable Notification ==
From '''Global settings''' - '''AS/AV''' - uncheck --> '''Send notification to recipient '''

= check zimbra service =
== Zimbra status before ==
{{{
zimbra@mail:/root$ zmcontrol status
Host mail.relay.noor
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
== Zimbra status after ==
{{{
zimbra@mail:/root$
zimbra@mail:/root$ zmcontrol status
Host mail.relay.noor
        antispam                Running
        ldap                    Running
        logger                  Running
        mailbox                 Running
        mta                     Running
        snmp                    Running
        spell                   Running
        stats                   Running
        zmconfigd               Running
}}}
