{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2012-12-26
Latest update: 2012-12-26
}}}
<<TableOfContents(4)>>

to create distribution list we will explain at this page how to create it and add members using command line

= Create distribution list =
== Create distribution list at Active Directory ==
1) Access our domain controller and add new distribution list

2) after creating it add new E-mail address (i.e. it@noor.net ) to be available for our users when searching about it through ldap accounts

== Add distribution list's members through Active Directory ==
1) Add distribution list's members in order to be documented

== Create distribution list at zimbra ==
1) we have created text files to be used for creating/modifying distribution list

2) these files located at zimbra mail server "/opt/zimbra/backup"

{{{
zimbra@smtp:/$ ls -l /opt/zimbra/backup/
total 116
-rw------- 1 zimbra zimbra  8591 2012-12-26 12:05 accounts-alias.txt
-rw------- 1 zimbra zimbra   827 2012-12-26 12:05 accounts-forward.txt
-rw-r----- 1 zimbra zimbra 13566 2012-12-25 20:44 all-accounts-and-forward.txt
-rw------- 1 zimbra zimbra  2161 2012-12-26 12:05 distribution-list-members-nooradsl.txt
-rw-r----- 1 zimbra zimbra 61896 2012-12-26 12:05 distribution-list-members.txt
-rw------- 1 zimbra zimbra   356 2012-12-26 12:05 distribution-list-nooradsl.txt
-rw-r----- 1 zimbra zimbra  6506 2012-12-26 12:05 distribution-list.txt
-rw-r----- 1 zimbra zimbra   230 2012-12-25 20:58 forward.txt
}}}
3) to create new distribution list, go this location and edit file "distribution-list.txt"

{{{
zimbra@smtp:/$ nano /opt/zimbra/backup/distribution-list.txt
}}}
4) add new distribution list:

{{{
createDistributionList it@noor.net
}}}
5) save file

6) ensure that you are switch to zimbra user and excute distribution list file which contains the new list:

{{{
su - zimbra

zmprov < /opt/zimbra/backup/distribution-list.txt
}}}
The list which are already exist will not be created but the new list will be created

== Add distribution list's members through zimbra ==
1) after adding users to domain controllar distribution group, access zimbra server

2) edit "distribution-list-members.txt" by adding new members to the created distribution list

{{{
 nano /opt/zimbra/backup/distribution-list-members.txt
}}}
3) add list members as below:

{{{
addDistributionListMember it@noor.net aali@noor.net
addDistributionListMember it@noor.net abadr@noor.net
addDistributionListMember it@noor.net amohsen@noor.net
addDistributionListMember it@noor.net ashehata@noor.net
addDistributionListMember it@noor.net atohamy@noor.net
addDistributionListMember it@noor.net hhassan@noor.net
addDistributionListMember it@noor.net kfarrag@noor.net
}}}
4) save file

5) excute "distribution-list-members.txt" file to add new members

{{{
su - zimbra

zmprov < /opt/zimbra/backup/distribution-list-members.txt
}}}
= Check created distribution list and added members =
1) to check distribution list and its members

{{{
zimbra@smtp:/$ zmprov gdl it@noor.net


# distributionList it@noor.net memberCount=7
cn: IT
description: IT-Team
displayName: IT
mail: it@noor.net
objectClass: zimbraDistributionList
objectClass: zimbraMailRecipient
uid: it
---> list restriction rules applied

zimbraACE: 00000000-0000-0000-0000-000000000000 all sendToDistList
zimbraACE: 302a8689-7b40-4950-a71f-329003790ce7 dom sendToDistList
zimbraACE: 1c28e868-6d72-4a58-bcea-f87267689d80 dom sendToDistList
zimbraACE: f9ee33a9-450f-42d8-bb37-bec267059866 dom sendToDistList
zimbraACE: 99999999-9999-9999-9999-999999999999 pub sendToDistList
zimbraCreateTimestamp: 20120409115551Z
zimbraId: c51269d2-6dce-4bfb-86cd-c39d31bdd1a8

---> list members
zimbraMailAlias: it@noor.net
zimbraMailForwardingAddress: aali@noor.net
zimbraMailForwardingAddress: abadr@noor.net
zimbraMailForwardingAddress: amohsen@noor.net
zimbraMailForwardingAddress: ashehata@noor.net
zimbraMailForwardingAddress: atohamy@noor.net
zimbraMailForwardingAddress: hhassan@noor.net
zimbraMailForwardingAddress: kfarrag@noor.net
zimbraMailStatus: enabled
}}}
= Remove distribution list =
== Remove distribution list from AD and Zimbra ==
1) remove distribution group from domain controllar

2) edit "distribution-list.txt" and remove row for this list

{{{
zimbra@smtp:/$ nano /opt/zimbra/backup/distribution-list.txt
}}}
remove this row

{{{
createDistributionList list@noor.net
}}}
3) run below command to remove distribution list

{{{
zimbra@smtp:/$ zmprov ddl list@noor.net
}}}
4) check removed list:

{{{
zimbra@smtp:/$ zmprov gdl list@noor.net

ERROR: account.NO_SUCH_DISTRIBUTION_LIST (no such distribution list: list@noor.net)
}}}
== Remove distribution list's member ==
1) to remove list member remove it first from domain controllar

2) edit file "distribution-list-members.txt" and remove member

{{{
nano /opt/zimbra/backup/distribution-list-members.txt
}}}
to remove atohamy, remove row :

{{{
addDistributionListMember it@noor.net atohamy@noor.net
}}}
{{{
addDistributionListMember it@noor.net aali@noor.net
addDistributionListMember it@noor.net abadr@noor.net
addDistributionListMember it@noor.net amohsen@noor.net
addDistributionListMember it@noor.net ashehata@noor.net

addDistributionListMember it@noor.net hhassan@noor.net
addDistributionListMember it@noor.net kfarrag@noor.net
}}}
3) excute below command to remove member

{{{
zimbra@smtp:/$ zmprov rdlm it@noor.net atohamy@noor.net
}}}
4) check current list members

{{{
zimbra@smtp:/$ zmprov gdl it@noor.net


# distributionList it@noor.net memberCount=6
cn: IT
description: IT-Team
displayName: IT
mail: it@noor.net
objectClass: zimbraDistributionList
objectClass: zimbraMailRecipient
uid: it

zimbraMailAlias: it@noor.net
zimbraMailForwardingAddress: aali@noor.net
zimbraMailForwardingAddress: abadr@noor.net
zimbraMailForwardingAddress: amohsen@noor.net
zimbraMailForwardingAddress: ashehata@noor.net

zimbraMailForwardingAddress: hhassan@noor.net
zimbraMailForwardingAddress: kfarrag@noor.net
zimbraMailStatus: enabled
}}}
