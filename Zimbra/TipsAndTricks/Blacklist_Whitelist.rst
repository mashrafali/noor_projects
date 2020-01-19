{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-04-23
Latest update: 2013-04-23
}}}
<<TableOfContents(3)>>

== Edit config file ==

{{{
vi /opt/zimbra/conf/salocal.cf.in 
}}}
and add the following lines:

{{{
blacklist_from black@test.com
whitelist_from white@test.com

also you can * to deny/allow all the domain members
}}}




{{{
#Sender Filtering
blacklist_from sattia@gmail.com
blacklist_from systems@noor.com
blacklist_from MatthewWisener@ncsevents.com
blacklist_from dina.fady@noorgroup.net
blacklist_from universal.researching@gmail.com
}}}



== Restart MTA service ==

after that restart mta service using zimbra user:
{{{
zmmtactl restart && zmamavisdctl restart
}}}
