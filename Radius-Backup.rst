#pragma section-numbers 2



{{{
Author       : Amr Mohsen 
Version      : 1
Creation date: 2013-4-16
Latest update: 2013-4-16
}}}

<<TableOfContents(4)>>
== How to connect to Radius Server ==

{{{
This server has two IP-address 
ssh root@41.187.101.60
OR
ssh root@172.17.14.18
}}}

== How to Backup Radius database ==
{{{
vim /usr/bin/radiusbe-bkp
}}}
{{{
date=$(date +"%m_%d_%Y")
mysqldump -u radius --password=radpass0wrd radiusbe >radiusbe_$date
}}}
{{{
press ESC --> :wq 
}}}
{{{
# to take backup daily at 1:00 PM
press "crontab -e" and at the last of page write this line
0 13 * * *      /usr/bin/radiusbe-bkp
}}}
{{{
to make this file executable 
chmod 777 /usr/bin/radiusbe-bkp
}}}
