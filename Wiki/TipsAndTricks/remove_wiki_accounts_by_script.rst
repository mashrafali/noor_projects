## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Wiki/TipsandTricks/remove_wiki_accounts_by_script
{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 2014-11-26
Latest update: 2014-11-26
}}}

<<TableOfContents(3)>>

== Script to delete unneeded users from Wiki ==

{{{

Put the unneeded users in /tmp/users

#!/bin/bash

for  mail  in  $(cat /tmp/users)
do
find /usr/local/share/moin/data/user -type f -exec grep -l "$mail" {} \;  >> /tmp/deletedusers
echo $mail delted
done

for userid in $(cat /tmp/deletedusers)
do
rm -rf $userid
done

}}}
