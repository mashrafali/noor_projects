{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-11-05
Latest update: 2014-11-05
}}}
<<TableOfContents(7)>>



== Remove physically server from Hsphere Control Panel ==

=== Preparation ===

 1. Disable server for sigup from hsphere E.Manager.
 1. Determine all logical servers configured at this physically server.<<BR>><<BR>> {{attachment:0.jpg}} <<BR>>
 1. Determine all accounts use physically/logical server.<<BR>><<BR>> {{attachment:01.jpg}} <<BR>>

=== Move websites and databases ===

 1. Move all accounts websites to the new web server then change DNS and apply changes for account after migration finished.<<BR>><<BR>>Hsphere has built in move account wizard to move account from web server to another web server with the same Operatin System<<BR>><<BR>> {{attachment:02.jpg}} <<BR>>
 1. Move all accounts databases to the new web server then change website's database configuration file to new database server.<<BR>><<BR>>http://wiki.noor.net/Enterprise/IT/Docs/HowTo/Linux/Hsphere/Tip_Tricks/Move_mysql_account<<BR>>
 
=== Check services on logical server ===

1. Check that there are no services still running on logical servers.<<BR>><<BR>>From E. Manger - Servers- L.Servers , if you see in the front of logical server the symbol "X" so server has not any services and you can remove it:<<BR>><<BR>> {{attachment:05.jpg}} <<BR>><<BR>>If are not allowed to remove it, so there is still accounts using this server, so search again at hsphere then move existing data or disable services (mysql or pgsql)<<BR>><<BR>>To disable mysql/pgsql service at account not having database, open account then under MySQL/PGSQL management Press "Green Button" to Disable service<<BR>><<BR>> {{attachment:3.jpg}} <<BR>><<BR>> {{attachment:4.jpg}} <<BR>>
 
=== Remove Logical Server ===

1. Remove logical server's IP address<<BR>><<BR>>Now you will see in the front of logical server the symbol "X" so server has not any services and it is ready to remove it.<<BR>><<BR>> {{attachment:5.jpg}} <<BR>><<BR>>If you press "X" you see the below error message so please edit logical server then remove IP address of its physical server<<BR>><<BR>> {{attachment:6.jpg}} <<BR>><<BR>>Go to Logical server and edit server to remove IP address configured<<BR>><<BR>> {{attachment:7.jpg}} <<BR>>
 1. Remove logical server<<BR>><<BR>> {{attachment:06.jpg}} <<BR>>
 
=== Delete Physical server ===

1. Delete physically server.<<BR>><<BR>>Now after logically servers removed you can go to physically server then you will find it "Unused" , so you can delete it easily:<<BR>><<BR>> {{attachment:9.jpg}} <<BR>><<BR>> {{attachment:10.jpg}} <<BR>><<BR>>Now you will see that Hspher removed Physically server and all its logical servers:<<BR>><<BR>> {{attachment:11.jpg}}
