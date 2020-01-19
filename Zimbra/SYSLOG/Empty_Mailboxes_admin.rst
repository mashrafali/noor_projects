Describe Enterprise/IT/Docs/HowTo/Linux/Zimbra/SYSLOG/Empty_Mailboxes_admin here.



{{{
Author       : Mahmoud Hamdy 
Version      : 1.0
Creation date: 5-11-2014
}}}


<<TableOfContents(3)>>

== empty syslog account for users ==

{{{
Make a secure shell session on the syslog server, then type su - zimbra -c "zmmailbox -z -m user@noornms.net emptyfolder inbox"

NOTE: this command will delete the filters of the user

}}}
