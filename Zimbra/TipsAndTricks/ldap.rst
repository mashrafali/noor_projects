{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-11-11
Latest update: 2014-11-11
}}}
<<TableOfContents(3)>>

if the zimbra ldap is not running , This problem occurred most of the time due to Server certificate is expired!

If there is no output, LDAP is not starting. 

If this line is present, verify that the zimbra system is detecting it (run as the zimbra user):

{{{
 	ldap status
}}}
A return of:

{{{
 	slapd running pid: 7568  (your PID will vary)

}}}

If you get no such response from the ldap status command, it's likely that the running slapd process is hanging around from a previous installation. To kill it manually:

{{{
 	 killall -TERM slapd
	 ps auxww | grep zimbra | grep slapd
}}}
If the process is still there, determine it's PID (second column in the ps output) and

{{{
 	kill -9 PID     --> (your PID will vary)
}}}
