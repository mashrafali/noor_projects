{{{
Author       : Mahmoud Hamdy 
Version      : 1.0
Creation date: 14-12-2014
}}}

<<TableOfContents(3)>>

== Scripts for Monitoring SIP servers channels to avoid Stuck calls ==

{{{

[root@elastix-2 ~]# less /usr/bin/sipchannelsmonitoring
/usr/bin/sipchannels
mutt -s "SIP channels monitoring" -a /root/channels it@noor.net < /root/itmail.txt
rm -rf /root/channels 


[root@elastix-2 ~]# less /usr/bin/sipchannels
asterisk -vvvvvrx 'core show channels verbose' > /root/channels

[root@elastix-2 ~]# less /root/itmail.txt 
Dear Team ,

Kindly find the attached SIP channels.


Best Regards

}}}
