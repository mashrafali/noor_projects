{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2015-09-13
Latest update: 2016-11-30
}}}
<<TableOfContents(3)>>

== Delete CLI ==

For SMTP.NOOR.NET
##############

As Zimbra --> Export list of messages ID
{{{
$ mailq | tail -n +2 | grep -v '^ *(' | awk  'BEGIN { RS = "" } { if ($8 == "spammers@noor.net" && $9 == "") print $1 } ' | tr -d '*!'  > /tmp/deletemsg
}}}

As root --> Delete theses messages ID
{{{
/opt/zimbra/postfix/sbin/postsuper -d - < /tmp/deletemsg
}}}

For more details --> https://wiki.zimbra.com/wiki/Managing-The-Postfix-Queues

== Delete with script ==
1- Create script & make it excutable

{{{
mail:/opt/zimbra/backup/scripts# touch /opt/zimbra/backup/scripts/delete-msg-queue 
mail:/opt/zimbra/backup/scripts# chown zimbra:zimbra delete-msg-queue 
mail:/opt/zimbra/backup/scripts# chmod a+x delete-msg-queue 
}}}

2- Modify created script as below


{{{
#!/usr/bin/perl -w
#
# pfdel - deletes message containing specified address from
# Postfix queue. Matches either sender or recipient address.
#
# Usage: pfdel <email_address>
#

use strict;

# Change these paths if necessary.
my $LISTQ = "/opt/zimbra/postfix/sbin/postqueue -p";
my $POSTSUPER = "/opt/zimbra/postfix/sbin/postsuper";

my $email_addr = "";
my $qid = "";
my $euid = $>;

if ( @ARGV !=  1 ) {
    die "Usage: pfdel <email_address>\n";
} else {
    $email_addr = $ARGV[0];
}

if ( $euid != 0 ) {
        die "You must be root to delete queue files.\n";
}


open(QUEUE, "$LISTQ |") ||
  die "Can't get pipe to $LISTQ: $!\n";

my $entry = <QUEUE>;    # skip single header line
$/ = "";        # Rest of queue entries print on
            # multiple lines.
while ( $entry = <QUEUE> ) {
    if ( $entry =~ / $email_addr$/m ) {
        ($qid) = split(/\s+/, $entry, 2);
        $qid =~ s/[\*\!]//;
        next unless ($qid);

        #
        # Execute postsuper -d with the queue id.
        # postsuper provides feedback when it deletes
        # messages. Let its output go through.
        #
        if ( system($POSTSUPER, "-d", $qid) != 0 ) {
            # If postsuper has a problem, bail.
            die "Error executing $POSTSUPER: error " .
               "code " .  ($?/256) . "\n";
        }
    }
}
close(QUEUE);

if (! $qid ) {
    die "No messages with the address <$email_addr> " .
      "found in queue.\n";
}

exit 0;
}}}


3- Run script to delete specific sender email address (As Root)

{{{
mail:/opt/zimbra/backup/scripts# ./delete-msg-queue SENDER
mail:/opt/zimbra/backup/scripts# /opt/zimbra/backup/scripts/delete-msg-queue MAILER-DAEMON
}}}

4- CHECK

{{{
mail:~# su - zimbra
 
zimbra@mail:~$ mailq | more
}}}
