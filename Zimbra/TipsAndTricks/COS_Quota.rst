{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-10-09
Latest update: 2014-10-09
}}}
<<TableOfContents(3)>>

== Create COS ==

{{{
zimbra@logs:~$ zmprov cc Syslog zimbraAttachmentsBlocked FALSE zimbraAuthTokenLifetime 60m zimbraMailQuota 6144 zimbraMailMessageLifetime 0
}}}

== Check created COS ==

{{{
zimbra@logs:~$ zmprov gac       
default
defaultExternal
syslog
}}}

== Edit COS ==

1- Quota= 6 GB
{{{
Home - Configure - COS - Edit - Advanced - Account quota (MB)=6144
}}}

2- Warning 70 %
{{{
Home - Configure - COS - Edit - Advanced - Percentage threshold for quota warning messages (%):70
}}}

3-  Quota warning message template:
{{{
Home - Configure - COS - Edit - Advanced - Quota warning message template:
}}}

{{{
From: IT Support <postmaster@${RECIPIENT_DOMAIN}>${NEWLINE}To: ${RECIPIENT_NAME} <${RECIPIENT_ADDRESS}>${NEWLINE}Subject: Quota warning${NEWLINE}Date: ${DATE}${NEWLINE}Content-Type: text/plain${NEWLINE}${NEWLINE}Your mailbox size has reached ${MBOX_SIZE_MB}MB, which is over ${WARN_PERCENT}% of your ${QUOTA_MB}MB quota.${NEWLINE}Please delete some messages to avoid exceeding your quota.${NEWLINE}Regards.${NEWLINE}IT/Systems Team.
}}}


== Move Mailbox to new COS ==
{{{
zmprov sac user@noornms.net syslog
}}}

If Mailbox is manually modified, Go to user (example: user@noornms.net)  and 

{{{
Edit - Advanced - Account quota (MB) - Reset to COS Value
}}}
