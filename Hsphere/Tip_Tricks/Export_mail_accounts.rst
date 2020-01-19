{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Export Mail Accounts for Specific Domain.
Creation date: 2016-03-24
Latest update: 2016-03-24
}}}
 . <<TableOfContents(4)>>

== Export mail accounts ==

=== Grep hsphere Control Panel database and password ===

 .{{{
less /hsphere/local/home/cpanel/hsphere/WEB-INF/classes/psoft_config/hsphere.properties | grep DB

DB_USER = wwwuser
DB_PASSWORD = password
}}}

=== Login to database ===

 .{{{
[root@cp ~]# psql -Uwwwuser hsphere
Password:


Welcome to psql 7.4.29, the PostgreSQL interactive terminal.

Type:  \copyright for distribution terms
       \h for help with SQL commands
       \? for help on internal slash commands
       \g or terminate with semicolon to execute query
       \q to quit
}}}

=== Export Mail Accounts ===

 .{{{
hsphere=# select full_email from mailboxes where full_email like '%DOMAIN%';
}}}

=== Example ===

To Export list of mail accounts for domain name grandinvestment.net

  .{{{
hsphere=# select full_email from mailboxes where full_email like '%grandinvestment.net%';
                full_email                
------------------------------------------
 postmaster@grandinvestment.net
 ameer@grandinvestment.net
 elhamy@grandinvestment.net
 info@grandinvestment.net
 m-mansour@grandinvestment.net
 nashat@grandinvestment.net
 tamer@grandinvestment.net
 waleed.badawy@grandinvestment.net
 support@grandinvestment.net
 grandinvestment.1143@grandinvestment.net
 capital@grandinvestment.net
 test@grandinvestment.net
-------------------------------------------
}}}
