{{{
Author       : Mohamed Ashraf
Title        : FortiNet Support Tickets
Version      : 1.0
Creation date: 13/9/2015
Latest update: 13/9/2015
}}}

<<TableOfContents(4)>>


= License Application =

== Support Chat ==

{{{
GoToAssist()
Welcome to Fortinet Chat

Chat with Previous Representative

Mohamed Ashraf(11:37:42)
hello

Nagaraj(11:37:46)
Hi

Mohamed Ashraf(11:38:07)
i encountered an issue while applying the license into my FG VM

Mohamed Ashraf(11:38:30)
the license has been registered successfully on the website, & i downloaded the file

Mohamed Ashraf(11:38:48)
however when i apply it unto my VM it shows invalid license ?

Mohamed Ashraf(11:40:08)
apologies for being straight forward, let me introduce myself, i am Mohamed, i work @NOOR Data networks (ISP) in Egypt

Mohamed Ashraf(11:40:23)
i am an Infrastrucutre Systems Engineer

Mohamed Ashraf(11:40:32)
so what is your opionion in this issue ?

Nagaraj(11:41:15)
We will have to check with Customer support team, if the license is valid

Nagaraj(11:41:24)
Let me transfer this request to CS team

Mohamed Ashraf(11:41:38)
do you require any further information ?

GoToAssist(11:41:43)
Nagaraj requested to transfer session to someone in portal Fortinet CS Chat.

Chat with New Representative

Sean(12:26:20)
Hi Mohamed

Mohamed Ashraf(12:26:31)
hello

Mohamed Ashraf(12:27:06)
there has been an update for this issue

Mohamed Ashraf(12:27:38)
did u read the history ?

Mohamed Ashraf(12:27:49)
hello ?

Sean(12:28:09)
Yes

Mohamed Ashraf(12:28:16)
ok :)

Mohamed Ashraf(12:28:33)
well i have successfully registered the license

Mohamed Ashraf(12:28:56)
after several reboots and re-uploading the file

Sean(12:28:56)
Oh, I was about to get to contact your SE

Sean(12:29:11)
That's great

Mohamed Ashraf(12:29:18)
there is another thing i would like to inquire about

Sean(12:30:00)
Yes, I'll do my best to help

Mohamed Ashraf(12:30:00)
#get system status   in CLI shows that my VM is multi tenant capable

Mohamed Ashraf(12:30:14)
however there were no option in gui for enabling vdoms

Sean(12:31:09)
I see

Sean(12:31:25)
I would suggest contacting your SE for technical assistance

Mohamed Ashraf(12:31:27)
i had to force enable in cli, please note that the command for enabling vdom "set vdom-admin enable"  was not listed in command list by entering "?"

Mohamed Ashraf(12:31:34)
but it accepted the command

Sean(12:31:47)
Alternatively, you could end this chat and start a new technical webchat

Mohamed Ashraf(12:31:51)
i would like to confirm that this VM is Multi Vdom capable

Mohamed Ashraf(12:32:20)
uhaa

Mohamed Ashraf(12:32:21)
isee

Mohamed Ashraf(12:32:28)
well thanks for your support

Mohamed Ashraf(12:32:31)
:)

Sean(12:32:35)
You're welcome

Mohamed Ashraf(12:32:44)
have a good day

Mohamed Ashraf(12:32:45)
vy

Mohamed Ashraf(12:32:47)
bye

Sean(12:32:51)
Thank you for using the Fortinet Web Chat Service! You can request a transcript of this Chat sent by email after leaving the session. A survey will appear after closing this chat window and we look forward receiving your feedback.

GoToAssist(12:33:10)
Mohamed Ashraf has left the session.

GoToAssist(12:33:11)
────────────────────────────────────────────────────────
────────────────────────────────────────────────────────
The session has ended. You can close this browser window.

You can save this conversation for future reference.
Save the chat history.

If our Mohamed Ashraf Survey did not open automatically, please click the following link:
Take our 1-minute Mohamed Ashraf Survey.https://broker.gotoassist.com/ds/sessionEndV8.flow?QueryKey=257848612&Portal=fortinetcschat&Token=257848612%3AutCpiNEKF7%2FHQK223aD3fw%3D%3D

}}}

= Dashboard Widgets Access Control =

== Support Chat ==

{{{
GoToAssist()
Welcome to Fortinet Chat

Inder(10:11:48)
Hi There

Inder(10:11:57)
How may I help you?

Mohamed Ashraf(10:12:18)
hello

Mohamed Ashraf(10:13:38)
For my fortiGateVM, i was inquiring if its possible to disable some Dashboard widgets for certain admin users

Inder(10:14:18)
firmware version ?

Mohamed Ashraf(10:14:43)
Version: FortiGate-VM64 v5.2.4,build0688,150722 (GA)

Inder(10:18:28)
You can go to the following commands.

#config system admin
#edit <name>
#get

Inder(10:18:37)
And then check the dashboard

Inder(10:18:40)
and press ?

Inder(10:18:54)
to check for the option unset etc.

Mohamed Ashraf(10:19:21)
NOOR-FG-1 (root) # config system admin 


NOOR-FG-1 (admin) # edit masriacars 


NOOR-FG-1 (masriacars) # get 
 

Mohamed Ashraf(10:19:37)
doesnt show anything.

Inder(10:20:30)
so check with other users there will be dashboard if configured.

Mohamed Ashraf(10:21:30)
NOOR-FG-1 (masriacars) # config dashboard


NOOR-FG-1 (dashboard) # show full-configuration 
config dashboard
    edit 7
        set widget-type sysop
        set name ''
        set tab-id 1
        set column 1
        set status open
    next
    edit 10
        set widget-type jsconsole
        set name ''
        set tab-id 1
        set column 1
        set status open
    next
    edit 9
        set widget-type tr-history
        set name ''
        set tab-id 1
        set column 1
        set interface "port2"
        set tr-history-period1 3600
        set tr-history-period2 86400
        set tr-history-period3 604800
        set refresh enable
        set status open
    next
    edit 1
        set widget-type sysinfo
        set name ''
        set tab-id 1
        set column 1
        set status open
    next
    edit 4
        set widget-type sysres
        set name ''
        set tab-id 1
        set column 2
        set time-period 1
        set chart-color 1
        set view-type real-time
        set cpu-display-type average
        set status open
    next
end


NOOR-FG-1 (dashboard) # get
== [ 7 ]
id: 7   
== [ 10 ]
id: 10   
== [ 9 ]
id: 9   
== [ 1 ]
id: 1   
== [ 4 ]
id: 4

Mohamed Ashraf(10:21:56)
can i force disable one of them ?

Mohamed Ashraf(10:22:05)
not allowing the user to add it again

Inder(10:22:51)
you can delete or unset them

Inder(10:23:09)
but do it for the user you want to do the thing.

Mohamed Ashraf(10:23:42)
but if i unset or delete, the user have privilage to add it again, correct ?

Inder(10:24:38)
depends on the permission you have configured for that user..you can edit the permission by going to the settings Config > User > Admin > Settings and then select the user for whom you want to change the permission.

Mohamed Ashraf(10:25:33)
which permission that affects widget modification ?

Inder(10:27:01)
Administrator Users

Mohamed Ashraf(10:27:03)
i can change permission in Global>Admin>Admin Profiles

Inder(10:27:14)
yes please

Mohamed Ashraf(10:27:42)
Access Control	 None	 Read Only	 Read-Write
System Configuration			
Network Configuration			
Administrator Users			
FortiGuard Update			
Maintenance			
Router Configuration			
Firewall Configuration			
Policy Configuration			
Address Configuration			
Service Configuration			
Schedule Configuration			
Packet Capture Configuration			
Other Configuration			
Security Profile Configuration			
AntiVirus			
Web Filter			
Application Control			
Intrusion Protection			
Email Filter			
Data Leak Prevention			
VoIP			
ICAP			
VPN Configuration			
User & Device			
WAN Opt & Cache			
Endpoint Security			
WiFi Controller			
Log & Report			
Configuration			
Data Access			
Report Access			
Threat Weight

Mohamed Ashraf(10:27:46)
which permission ?

Inder(10:29:16)
Administrator Users

Mohamed Ashraf(10:29:55)
ok hold on 1 sec

Inder(10:31:03)
ok

Mohamed Ashraf(10:31:30)
i set it to "None", but still user can delete & add new widgets

Mohamed Ashraf(10:31:33)
at dashboard

Mohamed Ashraf(10:31:55)
can i attach a picture for you here ?

Mohamed Ashraf(10:34:40)
http://postimg.org/image/bi9jaapnl/

Inder(10:35:25)
ok I see that Admin Users permission is still set to None so change it to R-W.

Mohamed Ashraf(10:39:04)
we need to prevent this user from adding "CLI Widget" @ his dashboard.

Mohamed Ashraf(10:39:06)
http://postimg.org/image/qq3uugwpn/full/

Inder(10:40:32)
ok have you changed the permission.

Mohamed Ashraf(10:41:06)
ok

Mohamed Ashraf(10:41:09)
then ?

Inder(10:41:49)
then check whether you are able to add those widgets for that user.

Mohamed Ashraf(10:42:26)
ok listen for a moment

Mohamed Ashraf(10:43:01)
the access profiles that i showed you, is not for ROOT, but for the user that i want to prevent him from adding widgets

Mohamed Ashraf(10:43:10)
i Do not want to add widgets for user

Mohamed Ashraf(10:43:26)
I want him Not being able to add widgets himself

Mohamed Ashraf(10:43:34)
Prevent him from adding widgets

Mohamed Ashraf(10:43:42)
Disable the user widgets for him

Mohamed Ashraf(10:44:33)
root add widgets yes , user add widgets no

Inder(10:47:00)
ok then set the permission for that user.

Mohamed Ashraf(10:47:07)
any solution for this ?

Inder(10:47:25)
for None or Read for that user

GoToAssist()
Representative is not having Internet connectivity problems any more.

GoToAssist()
Representative is not having Internet connectivity problems any more.

GoToAssist()
Representative is not having Internet connectivity problems any more.

Mohamed Ashraf(10:50:43)
ok i set it to none for user, but user can add widgets ok

Inder(10:51:29)
how about Read permission instead of None.

Mohamed Ashraf(10:51:43)
ok hold on

Inder(10:51:48)
ok

Mohamed Ashraf(10:53:08)
ok i set it to Read for user, but user can add widgets ok

Inder(10:53:59)
ok in that case I will suggest you to raise the case & open a new ticket with us. And mention what steps has been done in the chat.

Mohamed Ashraf(10:54:47)
ok thank you for your support

Mohamed Ashraf(10:54:49)
good bye

GoToAssist(10:54:56)
Mohamed Ashraf has left the session.

Inder(10:54:56)
ok bye

GoToAssist(10:54:56)
────────────────────────────────────────────────────────
────────────────────────────────────────────────────────
The session has ended. You can close this browser window.

You can save this conversation for future reference.
Save the chat history.

If our Mohamed Ashraf Survey did not open automatically, please click the following link:
Take our 1-minute Mohamed Ashraf Survey.https://broker.gotoassist.com/ds/sessionEndV8.flow?QueryKey=257935001&Portal=fortinettechchat&Token=257935001%3A1hooKjDuNlP7LfdGrueg6g%3D%3D

}}}

== Support Ticket #1484957 ==

=== Contact Information ===

Name:*	NOOR IT	

Email:*	it@noor.net	
 
Telephone:	33334999
	
Mobile Phone:	00201111692429	

Basic Information   ｜   Subject:   Dashboard Widgets Access Control for multi-Vdom Deployment

Ticket Number:	1484957	Serial Number:	FGVM080000043063

Status:	'''Closed'''	Ticket Priority:	P3

Creation Date:	2015-08-16	Close Date:	2015-08-19

S/W Version:	5.2 GA	Owner:	Rishikeshan Padinhare Madathil

Request Type:	Technical Assistance	Category:	FGT System

=== Problem Solution ===

Question/Misconfiguration: information in KB/docs

=== Ticket Conversation ===

{{{
	NOOR IT
2015-08-16 06:33:00	
Attachment:  NOOR-FG-1_20150816_1510.conf;  access-control.jpg
Dear Tech Support,

- We have a FortiGate VM deployed in NAT Multi Vdom Mode.

- The system info is as below:
Version: FortiGate-VM64 v5.2.4,build0688,150722 (GA)

- For a multi user access environment, we need to apply access controls on dashboard widgets for different user profiles.
For example, a user profile would have access to "interface history widget" but not "CLI Commands" Widget.

- For this purpose we have tried configuring User Profiles:
Global > Admin > admin Profiles > User profile
And marked "none" for Administrator Users, however the user is still able to add "CLI Widget"

- Find attached full user access control.

- Is this feature available?, and if so, how can it be applied via GUI or CLI.

Thanks in Advance,
}}}

{{{
	Rishikeshan Padinhare Madathil
2015-08-16 08:11:00	
Dear Customer, 

Thank you for contacting Fortinet Technical Support. 

If you set the action to "read only" for System configuration, you will still be able to add widget. 
Read only refers that it cannot make any configuration changes. 

Let me know if you have any queries. 

Regards, 

Rishi 
Fortinet Technical Support.
}}}

{{{
	NOOR IT
2015-08-16 23:13:00	
Attachment:  Sys-Config-None.jpg;  Sys-Config-ReadOnly.jpg
Dear Rishikeshan,

- By setting System Configuration to "none", this also disables the "FortiView" Tab which is essential for this profile.
- Find Attached the difference between the two in User POV.
- Is there another solution where we can keep FortiView and still apply above request ?

Thanks in Advance,
}}}

{{{
	Rishikeshan Padinhare Madathil
2015-08-19 03:34:00	
Hi Mohammad, 

Thank you for the time over the phone. 

Your requirement is that add widget option should be disabled when the admin profile is set to read only. 
As I explained "read only" action in the admin is only to limit the fortigate configuration. This will restrict in making any changes on the fortigate configuration such as adding a policy, removing the policy, configuring interface setting etc. Adding widget is a is not related to the read only action in admin setting. This is related to GUI feature. 
If you will still be able to add widget in read only. 

You can make this as a new feature request through the sales channel. or email to sales@fortinet.com 

Since your query is resolved, I am proceeding to close the ticket with your confirmation. 

Regards, 

Rishi 
Fortinet Technical Support.
}}}


= Point to Point Packet loss =

== Support Ticket #1491129 ==

=== Contact Information ===

Name:*	NOOR Systems	

Email:*	it@noor.net	
 
Telephone:	16700	

Mobile Phone:	00201111692429 or 00201006896691
	
Basic Information   ｜   Subject:   FortiGate VM Packet Losing on Point to Point ping

Ticket Number:	1491129	Serial Number:	FGVM080000043063

Status:	'''Closed'''	Ticket Priority:	P3

Creation Date:	2015-08-24	Close Date:	2015-08-25

S/W Version:	5.2 GA	Owner:	Somashekara Hanumantha Reddy

Request Type:	Technical Assistance	Category:	FGT Routing

=== Problem Solution ===

Not a Fortinet issue

=== Ticket Conversation ===

{{{
	NOOR Systems
2015-08-24 00:24:00	
Attachment:  Logs.txt;  Ping-results.txt
Dear Support,

- Our FortiGateVM has packet lose on point to point Ping.

- The physical cable is not the problem, because it loses packets on several links not just one.

- CPU/RAM is very normal, so we would like to find out why is the box causing packet loss.

- Is there any object or policy that could affect this ?

- Find attached Full Box Logs, and router point to point ping results.
}}}

{{{
	Somashekara Hanumantha Reddy
2015-08-24 22:55:00	
Dear Customer, 

Thank you for contacting Fortinet Technical Support. 

From the given information I could not understand the problem. 

To further assist you, kindly provide the below information. 

> is this a new configuration or it was working earlier 
> if it was working earlier, due to which configuration/network changes do you see this problem ? if yes kindly mention the changes 
> kindly attach the current configuration file and also mention the firewall policy id which is involved in this question 
> kindly attach the simple network diagram with IP schema and mention from which point to which point you have the packet loss problem 

When you are trying to ping, kindly capture the packets from the below command on the fortigate unit. 

diag sniff packet any 'host 172.16.30.2 and icmp' 4 0 a 

Regards, 

Somu 
Fortinet Technical Support.
}}}

{{{
	NOOR Systems
2015-08-25 01:05:00	
Attachment:  NOOR-FG-1_20150825_0957.conf;  FG-Network.jpg;  ICMP-Sniffer.pcap
Dear Support,

- Was working earlier, no significant change except adding two IPSEC VPN Tunnels to one of our VDOMs.

- In a New VDOM where there is no IPSEC, and allow all policy, this problem still exists.

- Configuration File & Network Design draft are attached.

- Sniffing Packets shows nothing useful during packet loss as the filter Does Not Show the Lost Packet.

- Attached is a Sniff Result during continuous ping where loss has occurred.

- please Escalate this issue as it is affecting ALL our VDOMS.
}}}

{{{
	Somashekara Hanumantha Reddy
2015-08-25 02:14:00	
Dear Customer, 

Thank you for the update. 

Please mention the firewall policy ID which is involved in this question and also capture the packets on Fortigate on respective VDOM when you try to ping the remote resource. 

Regards, 

Somu 
Fortinet Technical Support.
}}}

{{{
	NOOR Systems
2015-08-25 04:29:00	
Dear Support,

- After deep investigation, we found a configuration mismatch between our switches, which was causing the packet loss.

Consider this Ticket Closed.

Thanks for your help & apologies for any inconvenience.

Regards,
}}}

{{{
	Somashekara Hanumantha Reddy
2015-08-25 04:58:00	
Dear Customer, 

Thank you for the update. 

Glad to hear that your issue is resolved. 

I appreciate your efforts in identifying the problem. 

As per your confirmation, I am proceeding to close this case. 

Have a wonderful day. 

Regards, 

Somu 
Fortinet Technical Support.
}}}

= User VDOM - VPN Pure IPSEC Tunnel =

== Support Ticket #1490855 ==

=== Contact Information ===

Name: NOOR Systems

Email: it@noor.net
 
Telephone: 16700

Mobile Phone:	00201111692429

Basic Information   ｜   Subject:   User VDOM - VPN Pure IPSEC Tunnel

Ticket Number:	1490855	Serial Number:	FGVM080000043063

Status:	'''WaitGArelease'''	Ticket Priority:	P3

Creation Date:	2015-08-23	Close Date:	N/A

S/W Version:	5.2 GA	Owner:	Ahmed Wasfi

Request Type:	Bug	Category:	FGT VPN

=== Ticket Conversation ===

{{{
	NOOR Systems
2015-08-23 06:41:00	
Attachment:  ROOT-POV.jpg;  USER-VDOM-POV.jpg
Dear Tech Support,

- We have a FortiGate VM deployed in NAT Multi Vdom Mode.

- The system info is as below:
Version: FortiGate-VM64 v5.2.4,build0688,150722 (GA)

- For a multi Vdom Environment, One of our Users needed to create a VPN IPSEC Tunnel in his VDOM.

- The User could not find the Checkbox "Enable IPsec Interface Mode" in GUI, and so he is forced to create Interface mode tunnel, instead of pure IPSEC if he required.
Option under VPN>IPSEC>TUNNELS>Create New > Custom VPN Tunnel

- User Has Professional Admin privileges on his VDOM.

- Also when the User attempts to create the Tunnel via CLI, he could create both Interface mode or Tunnel Mode.

* Brief:
----------

ISSUE:
- GUI VDOM User Cannot choose between Interface mode IPSEC & Tunnel Mode.
- CLI VDOM User Can choose & Create normally.
Request:
- Why does this option not appear in GUI for VDOM USER? 

* Kindly find Attached Screenshots for this issue.

Thanks in Advance.
--
Mohamed Ashraf
Infrastructure Systems Engineer
}}}

{{{
	Amanpreet Kaur
2015-08-23 22:37:00	
Dear Customer, 

Thank you for contacting Fortinet Technical Support. 

This is Amanpreet Kaur and this ticket has been assigned to me. 

Issue: 

You are facing an issue with firewall blocking bandwidth. 

I tried calling on the number mentioned in the ticket but could not connect. 

Please mention your direct contact number so that I can call you. 

Action Plan: 

Please provide the configuration file on the ticket. 

Which vpn tunnels are configured : ssl /site to site? 

Also,provide the output of the following command : 

#get sys status 
get sys ha status 
diag sys top 1 20(run for 5 seconds and Ctrl+C to stop) 
get sys perf status 
diag hard sysinfo shm 
diagnose hardware sysinfo memory 
diag debug crashlog read 
exec tac report 
diagnose debug config-error-log read 
diagnose log alertconsole list 
diag sys flash list 
diag autoupdate versions 

================================================================================== 

Did you collect any logs at the time of the issue? 

How did you check firewall's cpu and memory at the time of the issue. 

At the time of the issue, please login through console and collect the below output: 

diag sys top 1 20 (run for 5 seconds and Ctrl+C to stop) 
diag sys top 2 
get sys perf status 
diag vpn ssl stats 
get sys session list 
get sys session stat 
diag hard sysinfo shm 
diagnose hardware sysinfo memory 
diag debug crashlog read 
exec tac report 
================================================================================================= 

Awaiting your response. 

Regards 
Amanpreet Kaur
}}}

{{{
	Amanpreet Kaur
2015-08-23 22:42:00	
please ignore the previous update
	Amanpreet Kaur
2015-08-23 23:46:00	
Dear Customer, 


Thank you for contacting Fortinet Technical Support. My name is Amanpreet Kaur and I will be assisting you on this ticket. 
================================================= 

Problem Summary:User VDOM - VPN Pure IPSEC Tunnel 
------------------------------------------------------------------------------------------- 

Next Action Plan: 

I have checked the same settings and prof admin wont be able to view " enable interface mode" option. 

To view the same , user should have super admin access. 

This is as per the current design and hence you need to change the admin priveledge to achieve the same. 

Please let me know if you have any further questions. 

Regards 
Amanpreet Kaur
}}}

{{{
	NOOR Systems
2015-08-24 00:11:00	
Attachment:  Logs.txt
Dear Amanpreet,

What do u mean with firewall blocking bandwidth ?
and does this affect the option of IPSEC explained above ?

*My direct Number is : 002-01111692429 (002) or (+2) is the country code.
*another Direct Number: 002-01006896691
- Please contact me at earliest possible.

- Find attached the requested logs.
}}}

{{{
	NOOR Systems
2015-08-24 00:14:00	
oh just noticed "ignore the previous update".

Ok no problem, but prof admin Can make that change via CLI, so the privilege is there

any ideas ?
}}}

{{{
	NOOR Systems
2015-08-26 00:25:00	
any update ?
}}}

{{{
	Amanpreet Kaur
2015-08-26 04:46:00	
Dear Customer, 

I am testing it on different version.On 5.2.3 , I am experiencing the same behaviour.I need to test this further. 

Once I have completed the testing, I would update with the test results at the earliest possible. 

Thanks 
Amanpreet Kaur
}}}

{{{
	Amanpreet Kaur
2015-08-26 05:37:00	
Dear Customer, 

Please attach the config file on the ticket. 

Regards 
Amanpreet Kaur
}}}

{{{
	NOOR Systems
2015-08-26 07:26:00	
Attachment:  NOOR-FG-1_20150826_1631.conf
Dear AmanPreet,

Find The Config File Attached as Requested.
}}}

{{{
	Subhashini Rajsingh
2015-08-27 00:00:00	
Dear Customer, 

Please be informed that we have escalated this ticket to next level. 

Regards, 
Subha 
Team Lead 
Fortinet Technical Support
}}}

{{{

	Pratheesh Kungatti
2015-08-30 01:42:00	
Dear Customer 

Ticket assigend to a level 2 engieer, he will analyze the issue and get back to you 

Regards, 

Pratheesh 
Fortinet Technical Support
}}}

{{{
	Ahmed Wasfi
2015-08-31 06:17:00	
Hello Eng. Mohamed, 

This ticket has been escalated to me for further assistance. 

I was able to replicate it in my LAB and I will submit this issue as bug. 

I will get back to you with more details soon. 

Regards, 

Ahmed Wasfi 
Fortinet Technical Support
}}}

{{{
	NOOR Systems
2015-08-31 06:44:00	
Dear Ahmed,

Thank you for your update, we are most glad to hear from you.

We will be waiting for your feedback regarding this issue.

Thanks in Advance.
Mohamed Ashraf
Infrastructure Systems Engineer
}}}

{{{
	Ahmed Wasfi
2015-09-01 04:03:00	
Hello Eng. Mohamed, 

A bug has been created and submitted to the developers regarding this issue: 

Bug ID: B0688 
Bug Summary: Missing "Enable IPsec Interface Mod" from GUI for pof_admin when VDOM enabled 

I will let you know the updates once I receive a feedback from the developers. 

Regards, 

Ahmed Wasfi 
Fortinet Technical Support
}}}

{{{
	Ahmed Wasfi
2015-09-02 01:16:00	
Hello Eng. Mohamed, 

The fix will be available on the new v5.4 firmware. 

We don't have a specific date for the release yet. 

As for now we can leave the ticket open and I will let you know whenever the firmware released. 

Regards, 

Ahmed Wasfi 
Fortinet Technical Support
}}}
