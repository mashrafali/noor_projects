{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Empty cPanel Temp.
Creation date: 2017-01-12
Latest update: 2017-01-12
}}}
 . <<TableOfContents(4)>>

== What is tempwatch ==
tempwatch a linux utility to cleanup temp files which have been accesses for given time.
Normally, it's used to clean up directories which are used for temporary holding space such as /tmp

== How it Works ==

Display tmp file

 {{{
ll /tmp/sess_0f*
}}}


 {{{
# ll /tmp/sess_0f*
-rw-------. 1 nzakiadmin nzakiadmin 172 Jan 11 17:13 /tmp/sess_0f7f2c8470bea31c75ea4c7cc8222f9f
-rw-------. 1 nzakiadmin nzakiadmin 172 Jan 11 15:02 /tmp/sess_0fa3dc27fd4f8b9cad6307351cbaeae9
-rw-------. 1 hpdadmin   hpdadmin     0 Jan 12 10:31 /tmp/sess_0fdb3dbfadb1087141eb4896dcff3998
}}}

To display what tempwatch will remove (Testing Mode)

 {{{
tmpwatch -t 12 /tmp
}}}

Set modification time, so tmpwatch will remove all files more than it

 {{{
tmpwatch -am 12 /tmp
}}}

or 
 {{{
tmpwatch --mtime --all 12 /tmp
}}}

 {{{
Switches used:
m - make the decision about deleting a file based on the file’s mtime
a - remove all file types 
}}}

Set crontab to schedule empty tmp files

 {{{
0 */4 * * * /usr/sbin/tmpwatch -am 12 /tmp
}}}

== Empty cPanel account Temp directory ==

Check tmp cPanel directory

 {{{
# du -hsc /home/username/tmp/ 
# du -hsc /home/misrt2admin/tmp/ 
}}}

To check all disk space by tmp

 {{{
# du -hsc  /home/*/tmp
}}}
 {{{
3.0M    /home/titanadmin/tmp
3.6M    /home/traceadmin/tmp
1.9M    /home/twistadmin/tmp
21M     /home/uniadmin/tmp
14M     /home/unitedadmin/tmp
14M     /home/urbanadmin/tmp
2.1M    /home/wayadmin/tmp
8.9M    /home/woodadmin/tmp
3.3M    /home/zabatnee/tmp
2.1G    total
 }}}

To remove tmp file for specific account

 {{{
tmpwatch --mtime --all 24 /home/username/tmp/
tmpwatch --mtime --all 24 /home/misrt2admin/tmp/ 
}}}
