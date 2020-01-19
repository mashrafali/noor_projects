{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2015-06-17
Latest update: 2015-06-17
}}}
<<TableOfContents(4)>>

===  Check current queue Life time ===

{{{
 ~]# less /var/qmail/control/queuelifetime 
604800  --> 1 week
}}}

=== Change Queue life time ===

To configure Qmail settings:


    From Control panel - Select E-Manager - Mail Servers  -> Servers menu:

	 Choose Mail server then above " queuelifetime " enter new value

{{{
   
    queuelifetime: the message queue lifetime in seconds. Default: 604800 (1 week).

Example:
========
172800  (2days)  (2*24*60*60)
}}}


=== restart qmail service ===

{{{
# /etc/init.d/qmaild restart
qmail stopping                                                                                  OK
qmail starting [ pop3 smtp submission ]   


# less /var/qmail/control/queuelifetime 
172800

}}}

=== Check mails at queue ===
Mail can be sent and received fine, but some messages are staying in queue:

{{{
 ~]# /var/qmail/bin/qmail-qstat
messages in queue: 142
messages in queue but not yet preprocessed: 3
}}}

For hsphere

{{{
 # /hsphere/local/var/qmail/bin/qmail-qstat
}}}

To list multiple messages in the queue, type:
{{{
   # /var/qmail/bin/qmail-qread
}}}
For hsphere

{{{
 # /hsphere/local/var/qmail/bin/qmail-qread
}}}

=== Force current queued message ===
If you wish to force Qmail to process mail that is staying in queue, 
send ALRM signal to the qmail-send process, 
and Qmail will try to send all messages from the queue immediately:

{{{
 ~]# kill -ALRM `ps ax | grep qmail-send | grep -v grep | awk '{print $1}'`

}}}

Example (details)

{{{
# ps ax | grep [q]mail-send
 4439 pts/3    S+     0:00 grep qmail-send
16800 pts/3    S      0:00 qmail-send

# ps ax | grep [q]mail-send | awk '{print $1}'

4088
16800
# kill -ALRM PIDNUMBER
# kill -ALRM 16800
or
# kill -s ALRM 16800
}}}
