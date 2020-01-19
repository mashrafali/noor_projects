{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Export Mail Accounts for cPanel.
Creation date: 2016-03-24
Latest update: 2016-03-24
}}}
 . <<TableOfContents(4)>>

== Export mail accounts ==

=== Method 1 ===

 .{{{
cat /etc/userdomains | sed "s/://g" | awk {'system("ls -1d /home/"$2"/mail/"$1"/* 2> /dev/null")'} | sed "s/\// /g" | awk {'print $5"@"$4'}
}}}

 .{{{
cat /etc/userdomains | sed "s/://g" | awk {'system("ls -1d /home/"$2"/mail/"$1"/* 2> /dev/null")'} | sed "s/\// /g" | awk {'print $5"@"$4'} > /tmp/allusers.txt
}}}

=== Method 2 ===

 1- create AWK file

 .{{{
nano getAllEmail.awk
}}}

 .{{{
BEGIN {
}
{
tcmd = "test -d " $1
if(!system(tcmd)){
split($1,MyArray,"/")
print MyArray[6] "@" MyArray[5]
}
}
}}}

2- Export mail accounts

A-- For all domains
 .{{{
ls -d /home/*/mail/*/*/ | awk -f getAllEmail.awk > /tmp/emailaddress.txt
}}}

B-- For specific cPanel account

 .{{{
ls -d /home/[USERNAME]/mail/*/*/ | awk -f getAllEmail.awk > /tmp/emailaddress.txt
}}}

=== Example ===

To Export list of mail accounts for cPanel account ecardadmin

  .{{{
[~]# ls -d /home/ecardadmin/mail/*/*/ | awk -f getAllEmail.awk
administrative@ecard.com.eg
ahmed.adel@ecard.com.eg
alexandria@ecard.com.eg
amr.ghalwash@ecard.com.eg
assuit@ecard.com.eg
benisweif@ecard.com.eg
cars@ecard.com.eg
commercial@ecard.com.eg
damietta@ecard.com.eg
ecard@ecard.com.eg
elbehaira@ecard.com.eg
eldakahlia@ecard.com.eg
elgharbeia@ecard.com.eg
elismailia@ecard.com.eg
elkalyobeia@ecard.com.eg
elminia@ecard.com.eg
elmonofia@ecard.com.eg
elsharkia@ecard.com.eg
financial@ecard.com.eg
gehad.mohamed@ecard.com.eg
hr@ecard.com.eg
info@ecard.com.eg
it@ecard.com.eg
kafr-elsheikh@ecard.com.eg
mahmoud.alsadny@ecard.com.eg
mahmoudfoda@ecard.com.eg
noha.nashaat@ecard.com.eg
sherif.samy@ecard.com.eg
}}}
