## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Zimbra/Tips_tricks/Restrict_dist_group
## page was renamed from Enterprise/IT/Docs/How_To/Linux/Zimbra/Zimbra_tips_tricks/Restrict_dist_group
{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2012-12-12
Latest update: 2014-02-26
}}}
<<TableOfContents(3)>>

= Enable the Milter service =
In ZCS 7.x, you now have the ability to protect your distributions lists (DL) from unwanted senders.

First you need to enable the Milter service.

'''__A- From Zimbra GUI__''' :

{{{
     Global: Home > Configure > Global Settings > MTA > Milter Server
}}}
{{{
     Server: Home > Configure > Servers > smtp.noor.net > MTA > Milter Server
}}}
To start the milter manually:

{{{
        $ zmmilterctl start
}}}
To check the status of the milter:

{{{
        $ zmmilterctl status
}}}
'''__B- From command Line__'''

{{{
# su - zimbra

$ zmprov modifyConfig zimbraMilterServerEnabled TRUE
$ zmprov modifyServer smtp.noor.net zimbraMilterServerEnabled TRUE
$ zmmilterctl start
$ zmmilterctl status
}}}
Please check if all services are running now as expected. If the output of

{{{
zmcontrol status
}}}
is something like

{{{
zimbra@mainzimbra:/root$ zmcontrol status
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
you can start the Milter Server with it's own command:

{{{
zmmilterctl start
}}}
Now all necessary services should be up and running. You can double check this in the Admin GUI.

= Restrict group =
1- Restrict list

If you already have a DL, create an access control list giving  members of the DL the ability to send messages to the DL, and only  members of the DL.

{{{
zmprov grr dl list@domain.com grp list@domain.com sendToDistList
}}}
Check the permissions of a member address in the list.

{{{
zmprov ckr dl list@domain.com jsmith@domain.com sendToDistList
}}}
= Restrict user =
2- Restirct user

to allow only specific user to send to distribution group

{{{
zmprov grr dl list@domain usr user@domain sendToDistList
}}}
Check the permissions of a user in the list.

{{{
zmprov ckr dl list@domain.com user@domain.com sendToDistList
}}}
= Allow all users =
3- Allow all users

- To allow all users in a domain:

{{{
  zmprov grr dl group@test.com dom test.com sendToDistList
}}}
- To allow all internal users

{{{
  zmprov grr dl group@test.com all sendToDistList
}}}
- To allow all public email addresses

{{{
  zmprov grr dl group@test.com pub sendToDistList
}}}
- Grant rights to an individual user in a domain to send messages to alldistribution lists.

{{{
zmprov grr domain <domain_name> usr <user1@example.com>sendToDistList
}}}
- Grant rights to all users in a domain to send messages to all distributionlists.

{{{
zmprov grr domain <domain_name> dom <example.com> sendToDistList
}}}
= allow external user =
- To allow a specific external email address

{{{
  zmprov grr dl group@test.com gst someone@domain "" sendToDistList
}}}
= Disable user =
Disable an address from sending to a list.

{{{
zmprov grr dl listname@domain usr user@domain -sendToDistList
}}}
= Example =
== everyone ==
1-Everyone@noor.net

{{{
zimbra@smtp:/root$ zmprov cdl everyone_restrict@noor.net
638f897c-8223-4022-80ff-65d75038fba7

zimbra@smtp:/root$ zmprov adlm everyone_restrict@noor.net ceo@noor.net
zimbra@smtp:/root$ zmprov adlm everyone_restrict@noor.net baseld@noor.net
zimbra@smtp:/root$ zmprov adlm everyone_restrict@noor.net basel@noor.net
zimbra@smtp:/root$ zmprov adlm everyone_restrict@noor.net cto@noor.net
zimbra@smtp:/root$ zmprov adlm everyone_restrict@noor.net eolivier@noor.net
zimbra@smtp:/root$ zmprov adlm everyone_restrict@noor.net hr@noor.net
zimbra@smtp:/root$ zmprov adlm everyone_restrict@noor.net it@noor.net
}}}
{{{
zimbra@smtp:/root$ zmprov grr dl everyone@noor.net grp everyone_restrict@noor.net sendToDistList
}}}
{{{
zimbra@smtp:/root$ zmprov ckr dl everyone@noor.net everyone_restrict@noor.net sendToDistList
DENIED
}}}
{{{
zimbra@smtp:/root$ zmprov ckr dl everyone@noor.net ceo@noor.net sendToDistList
ALLOWED
Via:
    target type  : dl
    target       : everyone@noor.net
    grantee type : grp
    grantee      : everyone_restrict@noor.net
    right        : sendToDistList
}}}
{{{
zimbra@smtp:/root$ zmprov ckr dl everyone@noor.net cto@noor.net sendToDistList
ALLOWED
Via:
    target type  : dl
    target       : everyone@noor.net
    grantee type : grp
    grantee      : everyone_restrict@noor.net
    right        : sendToDistList
}}}
{{{
zimbra@smtp:/root$ zmprov ckr dl everyone@noor.net hr@noor.net sendToDistList
ALLOWED
Via:
    target type  : dl
    target       : everyone@noor.net
    grantee type : grp
    grantee      : everyone_restrict@noor.net
    right        : sendToDistList
}}}
== Engineering ==
2- Engineering@noor.net

{{{
zimbra@smtp:/root$ zmprov grr dl engineering@noor.net all sendToDistList
}}}
{{{
zimbra@smtp:/root$ zmprov grr dl engineering@noor.net dom noor.net sendToDistList
}}}
{{{
zimbra@smtp:/root$ zmprov ckr dl engineering@noor.net cs@nooradsl.com sendToDistList
ALLOWED
Via:
    target type  : dl
    target       : engineering@noor.net
    grantee type : all
    grantee      :
    right        : sendToDistList
}}}
{{{
zimbra@smtp:/root$ zmprov ckr dl engineering@noor.net cto@noor.net sendToDistList
ALLOWED
Via:
    target type  : dl
    target       : engineering@noor.net
    grantee type : dom
    grantee      : noor.net
    right        : sendToDistList
}}}
= Reload Configuration =
 * After granting or revoking rights for the milter, you must reaload the configuration for the changes to take effect.

{{{
          $ zmmtactl reload
}}}
= Remove Milter service =
REMOVE MILTER SERVICE

{{{
zimbra@mainzimbra:/root$ zmprov ms 'zimbrahostname' zimbraMtaSmtpdMilters ""
zimbra@mainzimbra:/root$ zmprov ms 'zimbrahostname' zimbraMtaNonSmtpdMilters ""
zimbra@mainzimbra:/root$ zmmtactl reload
}}}
{{{
zimbra@mainzimbra:/root$ zmprov ms smtp.noor.net zimbraMtaSmtpdMilters ""
zimbra@mainzimbra:/root$ zmprov ms smtp.noor.net zimbraMtaNonSmtpdMilters ""
zimbra@mainzimbra:/root$ zmmtactl reload
}}}
