{{{
Author       : Karim Farrag
Version      : 1.1
Creation date: 2015-6-08
Latest update: 2015-6-08
}}}
To empty certain Mailbox content older than a certain period of time use the following command:
{{{
find /home/elsewedy/mail/.spamgrp@elsewedyindustries_com/cur new -name '????*' -mtime +3 | xargs rm -f > /dev/null 2>&1
}}}
if you want that to be scheduled add the command to the crontab
{{{
0 1 * * * find /home/elsewedy/mail/.spamgrp@elsewedyindustries_com/cur new -name '????*' -mtime +3 | xargs rm -f > /dev/null 2>&1
}}}
