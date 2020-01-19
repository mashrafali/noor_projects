{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-07-13
Latest update: 2014-07-13
}}}
<<TableOfContents(3)>>

== Disable single mail account ==
To disable single user at Cpanel, you need only to comment user's data (3 files) at cpanel server using CLI :

{{{
1- # /home/user/etc/domainname/passwd
}}}
Example

{{{
        # vi /home/elsewedy/etc/elsewedytrading.com/passwd
                #setco:x:502:500::/home/elsewedy/mail/elsewedyind.com/setco:/home/elsewedy
}}}
{{{
2- # /home/user/etc/domainname/shadow
}}}
Example

{{{
        # vi /home/elsewedy/etc/elsewedytrading.com/shadow
                #setco:$1$WZHTyFXW$98FRFGQV/Uqf2yj3ndqBw1:15907:::::::
}}}
{{{
3- # /home/user/etc/domainname/quota
}}}
Example

{{{
        # vi /home/elsewedy/etc/elsewedytrading.com/quota
                #setco:1048576000
}}}
== Enable single mail account ==
To enable single user at Cpanel, you need only to uncomment user's data (3 files) at cpanel server using CLI
