## page was renamed from Enterprise/IT/Docs/TipsAndTricks/SIPserverreports
{{{
Author       : Ahmed Shehata
Version      : 1.0
Creation date: 2013-11-3
Latest update: 2013-11-3
}}}


= SIP Server Reports =

Path to all scripts:

{{{
\\nooreng\Common\Ahmed Shehata\SIP scripts.rar
}}}
<<BR>>
We have many reports for the following teams :
<<BR>>
<<BR>>
1- ETS1. (5 Reports)
<<BR>>
<<BR>>
2- CTS1. (7 Reports)
<<BR>>
<<BR>>
3- CS. (2 Reports)
<<BR>>

= Reports Generation and sending sequence =
- Query statement script.
- Send the generated reports. (mutt)
- Delete the generated reports.(rm -rf /path of reports)

<<BR>>
Example:
<<BR>>
{{{
ets1-indvidual
mutt -s "Daily Call report" -a /root/ets1/750 -a /root/ets1/754 -a /root/ets1/756 -a /root/ets1/751 -a /root/ets1/752 -a /root/ets1/753 -a /root/ets1/755 -a /root/ets1/757 -a /root/ets1/758 -a /root/ets1/759 -a /root/ets1/760 -a /root/ets1/762 -a /root/ets1/764 -a /root/ets1/765 -a /root/ets1/768 -a /root/ets1/769 -a /root/ets1/770  mwahba@noor.net it@noor.net </root/wahbamail.txt
rm -rf /root/ets1/*

}}}


= Sending Reports by Mails =

<<BR>>
We use '''mutt''' to attach reports and send it by mail.
<<BR>>

{{{

mutt -s "Mail Subject" -a "path of the file to attach" ashehata@noor.net < "path to massage body"
}}}


= 1- ETS1 Reports =

<<BR>>

1 - 
{{{
/usr/bin/wahbainvid.sh
}}}
This Script send daily each agent calls reports (Answered , No Answered & busy).
<<BR>>
- Sample of query statement: 
<<BR>>
{{{
mysql -u root --password=****** asteriskcdrdb -e 
'select src,dst,disposition,calldate,duration from cdr 
where (dst  like "750%")
and (disposition like "ANSWERED%") 
and calldate > DATE_SUB(NOW(), INTERVAL 1 DAY) ;' >> /root/ets1/750

}}}

2 - 
{{{
/usr/bin/wahbareg.sh
}}}
This Script send daily each agent (registered &Unregistered) event.
<<BR>>
- Sample of query statement: 
<<BR>>

{{{
cat  /var/log/asterisk/full.1    | grep "Registered SIP '750'"  >> /root/ets1/ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '750'"  >> /root/ets1/ets1-register-report.txt
}}}

3 - 
{{{
/usr/bin/wahbafailed.sh
}}}
This Script send daily each agent failed calls, this happen when the agent use (Don't disturb) option at the soft phone. 
<<BR>>
- Sample of query statement: 
<<BR>>


{{{

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "758%") and (disposition like "FAILED%") and calldate > DATE_SUB(NOW(), INTERVAL 1 DAY) ;' >> /root/ets1/758
}}}

4 - 
{{{
/usr/bin/wahbaweekly.sh 
}}}
This Script send the following reports weekly:
<<BR>>'''outgoing-only-customer'''<<BR>>
<<BR>>
- Sample of query statement: 
<<BR>>

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "8%") and disposition like "%ANSWERED" and (length(dst)>4) and (src between 750 and 770) and calldate > DATE_SUB(NOW(), INTERVAL 1 week) order by 1;' > /root/ets1/outgoing-only-customer.txt
}}}

<<BR>>'''outgoing-only-tech'''<<BR>>
<<BR>>
- Sample of query statement: 
<<BR>>
{{{
mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where ( dst between 159 and 170) and disposition like 
"%ANSWERED" and (src between 750 and 770) and calldate > DATE_SUB(NOW(), INTERVAL 1 week) order by 2 ;'> /root/ets1/outgoing-only-tech.txt
}}}

<<BR>>'''incoming-only-tech'''<<BR>>
<<BR>>
- Sample of query statement: 
<<BR>>
{{{

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where( src between 159 and 170) and (dst between 750 and 
770) and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+6 DAY and calldate<=curdate() and disposition like "%ANSWERED" order by 2 ;'> /root/ets1/incoming-only-tech.tx
}}}


<<BR>>'''incoming-only-customer'''<<BR>>
<<BR>>
- Sample of query statement: 
<<BR>>
{{{
mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where( src between 159 and 170) and (dst between 750 and 
770) and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+6 DAY and calldate<=curdate() and disposition like "%ANSWERED" order by 2 ;'> /root/ets1/incoming-only-tech.tx
}}}


5 - 
{{{
/usr/bin/wahbamonthly.sh
}}}

<<BR>>

- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "756%") and (disposition like "NO ANSWER%") and calldate > DATE_SUB(NOW(), INTERVAL 1 month) ;' >> /root/ets1/756
}}}





= 2- CTS1 Reports =

1- hamin-out-count.sh

<<BR>>
This script generate the count of outgoing daily calls
<<BR>>

- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select disposition,count(src)from cdr where (src  like "782%") and (disposition like "NO ANSWER%") and calldate > DATE_SUB(NOW(), INTERVAL 1 DAY) ;' >> /root/cts/782
}}}


2- hamin-incom-count.sh


<<BR>>
This script generate the count of incoming daily calls
<<BR>>


- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select disposition,count(src) from cdr where (dst  like "782%") and (disposition like "NO ANSWER%") and calldate > DATE_SUB(NOW(), INTERVAL 1 DAY) ;' >> /root/cts/782
}}}

3- hamin-incom-count-weekly.sh

<<BR>>
This script generate the count of incoming weekly calls
<<BR>>


- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select disposition,count(src) from cdr where (dst  like "782%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+6 DAY ;' >> /root/cts/782
}}}


4- hamin-out-count-weekly.sh

<<BR>>
This script generate the count of outing weekly calls
<<BR>>

- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select disposition,count(src),sum(duration)/60,sum(duration)/(count(src)*60) from cdr where (src  like "788%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+6 DAY ;' >> /root/cts/788
}}}


5- hamin-out-count-monthly.sh

<<BR>>
This script generate the count of outing monthly calls
<<BR>>

- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select disposition,count(src)from cdr where (src  like "788%") and (disposition like "NO ANSWER%") and calldate > DATE_SUB(NOW(), INTERVAL 30 DAY) ;' >> /root/cts/788
}}}


6- hamin-incom-count-monthly.sh

<<BR>>
This script generate the count of incoming monthly calls
<<BR>>

- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select disposition,count(src) from cdr where (dst  like "788%") and (disposition like "NO ANSWER%") and calldate > DATE_SUB(NOW(), INTERVAL 30 DAY) ;' >> /root/cts/788
}}}



= 3- CS Reports =

1- cs-report-send
<<BR>>
This Script send daily each agent calls reports (Answered , No Answered & busy).
<<BR>>

- Sample of query statement: 

{{{
mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "512%") and (disposition like "NO ANSWER%") and calldate > DATE_SUB(NOW(), INTERVAL 1 DAY) ;' >> /root/cs/512
}}}

2- cs-incom-count-weekly.sh

<<BR>>
This script generate the count & sum of calls duration & calls average of incoming weekly calls
<<BR>>

- Sample of query statement: 

{{{

mysql -u root --password=****** asteriskcdrdb -e 'select disposition,count(src),sum(duration)/60,sum(duration)/(count(src)*60) from cdr where (dst  like "514%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+6 DAY ;' >> /root/cs/514
}}}
