{{{
Author       : Ahmed AbdelSattaar
Version      : 1.1
Creation date: 2014-10-27
Latest update: 2015-04-09
}}}

=== Webmine :Configure DNS1 & DNS0 as Slave for master server ===

- Loging to webmin using web browser with the following URL
{{{
https://172.17.13.10:10000

}}}
- Then Edit the following webmin setting
{{{
Webmin Servers Index --> Scan for Webmin Servers
}}}

{{{
Broadcast for server : Broadcasting for servers on addresses 172.17.255.255 , 255.255.255.255 , 172.17.13.255 ..

Found this server at https://172.17.13.10:10000/

Found new server at https://172.17.13.38:10000/
Found new server at https://172.17.30.118:10000/
}}}

- Press "Edit" for two servers (172.17.13.38 - 172.17.30.118)
{{{
Server details
Hostname or IP address 	
Port 	
Server type 	:Ubuntu Linux
SSL server? 	: Yes
Description 	From hostname 
Member of server groups 	New group
Link type 	Normal link to server :	Login via Webmin with username (root/password)
Save.
}}}

- Under Servers Edit the following setting
{{{
Servers -->  Bind -->  Cluster Slave Servers
}}}

{{{
Add server 

172.17.13.38
-----------------	
View on slaves to add zones to 	: At top level

Create secondary on slave when creating locally? 	: Yes
Create all existing master zones on slave? 	:yes
Name for NS record 	: dns1.noor.net (this is the hostname for first NS which will added to every zone)

Press: Add
}}}

{{{
Add server 
172.17.30.118
------------------	
View on slaves to add zones to 	: At top level

Create secondary on slave when creating locally? 	: Yes
Create all existing master zones on slave? 	:yes
Name for NS record 	: dns0.noor.net (this is the hostname for second NS which will added to every zone)

Press: Add
}}}
