{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 2014-11-26
Latest update: 2014-11-26
}}}


'''Script to delete unneeded users from Wiki'''<<br>>

{{{

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
