{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : cPanel Exim mail server Queue.
Creation date: 2016-08-15
Latest update: 2016-08-15
}}}
 . <<TableOfContents(4)>>

== Basic information ==

Print a count of the messages in the queue:

{{{
# exim -bpc
}}}

Print a listing of the messages in the queue (time queued, size, message-id, sender, recipient):

{{{
# exim -bp
}}}

Print a summary of messages in the queue (count, volume, oldest, newest, domain, and totals):

{{{
# exim -bp | exiqsumm
}}}

Print what Exim is doing right now:
{{{
# exiwhat
}}}


== Searching the queue with exiqgrep ==

Exim includes a utility that is quite nice for grepping through the queue, called exiqgrep. Learn it. Know it. Live it. If you're not using this, and if you're not familiar with the various flags it uses, you're probably doing things the hard way, like piping `exim -bp` into awk, grep, cut, or `wc -l`. Don't make life harder than it already is.

First, various flags that control what messages are matched. These can be combined to come up with a very particular search.

Use -f to search the queue for messages from a specific sender:

{{{
# exiqgrep -f [luser]@domain
}}}

Use -r to search the queue for messages for a specific recipient/domain:
{{{
# exiqgrep -r [luser]@domain
}}}

Use -o to print messages older than the specified number of seconds. For example, messages older than 1 day:
{{{
# exiqgrep -o 86400 [...]
}}}

Use -y to print messages that are younger than the specified number of seconds. For example, messages less than an hour old:
{{{
# exiqgrep -y 3600 [...]
}}}


Use -s to match the size of a message with a regex. For example, 700-799 bytes:
{{{
# exiqgrep -s '^7..$' [...]
}}}


Use -z to match only frozen messages, or -x to match only unfrozen messages.

There are also a few flags that control the display of the output.


Use -i to print just the message-id as a result of one of the above two searches:
{{{
# exiqgrep -i [ -r | -f ] ...
}}}


Use -c to print a count of messages matching one of the above searches:
{{{
# exiqgrep -c ...
}}}

Print just the message-id of the entire queue:
{{{
# exiqgrep -i
}}}


== Managing the queue ==

The main exim binary (/usr/sbin/exim) is used with various flags to make things happen to messages in the queue. Most of these require one or more message-IDs to be specified in the command line, which is where `exiqgrep -i` as described above really comes in handy.

Start a queue run:
{{{
# exim -q -v
}}}

Start a queue run for just local deliveries:
{{{
# exim -ql -v
}}}

Remove a message from the queue:
{{{
# exim -Mrm <message-id> [ <message-id> ... ]
}}}


Freeze a message:
{{{
# exim -Mf <message-id> [ <message-id> ... ]
}}}

Thaw a message:
{{{
# exim -Mt <message-id> [ <message-id> ... ]
}}}

Deliver a message, whether it's frozen or not, whether the retry time has been reached or not:
{{{
# exim -M <message-id> [ <message-id> ... ]
}}}

Deliver a message, but only if the retry time has been reached:
{{{
# exim -Mc <message-id> [ <message-id> ... ]
}}}

Force a message to fail and bounce as "cancelled by administrator":
{{{
# exim -Mg <message-id> [ <message-id> ... ]
}}}

Remove all frozen messages:
{{{
# exiqgrep -z -i | xargs exim -Mrm
}}}

Remove all messages older than five days (86400 * 5 = 432000 seconds):
{{{
# exiqgrep -o 432000 -i | xargs exim -Mrm
}}}

Freeze all queued mail from a given sender:
{{{
# exiqgrep -i -f luser@example.tld | xargs exim -Mf
}}}

View a message's headers:
{{{
# exim -Mvh <message-id>
}}}

View a message's body:
{{{
# exim -Mvb <message-id>
}}}

View a message's logs:
{{{
# exim -Mvl <message-id>
}}}

Add a recipient to a message:
{{{
# exim -Mar <message-id> <address> [ <address> ... ]
}}}

Edit the sender of a message:
{{{
# exim -Mes <message-id> <address>
}}}

== REMOVE ALL MESSAGES FROM QUEUE ==

To remove all messages from the sender, enter:
{{{
 # exiqgrep -i -f user@domain | xargs exim -Mrm
}}}

Remove all frozen messages:
{{{
# exiqgrep -z -i | xargs exim -Mrm
}}}

run the below command to delete all undelivered emails.
{{{
# exiqgrep -i -f "<>" | xargs exim -Mrm
}}}

To remove all messages from the queue, enter:
{{{
# exim -bp | awk '/^ *[0-9]+[mhd]/{print "exim -Mrm " $3}' | bash
}}}

OR 

You can use the suggested following clean command:
{{{
# exim -bp | exiqgrep -i | xargs exim -Mrm
}}}
