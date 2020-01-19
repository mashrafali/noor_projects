#pragma section-numbers 2



{{{
Author       : Amr Mohsen 
Version      : 1
Creation date: 2013-4-16
Latest update: 2013-4-16
}}}

<<TableOfContents(4)>>

== How to Generate Elastix report ==

{{{
ssh root@192.168.10.4
}}}
== to generate ets1-register script ==
{{{
#run this command 
#ets1-register-report
#that is command contain
cat  /var/log/asterisk/full.1    | grep "Registered SIP '750'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '750'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '752'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '752'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '753'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '753'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '755'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '755'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '756'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '756'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '758'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '758'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '759'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '759'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '760'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '760'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '764'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '764'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '765'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '765'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '768'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '768'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '769'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '769'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Registered SIP '770'"  >> ets1-register-report.txt
cat  /var/log/asterisk/full.1    | grep "Unregistered SIP '770'"  >> ets1-register-report.txt
}}}

== how to generate ets1 call report for extensions individual for the last day only ==
{{{ 
#run this command
#ets1-indvidual
{{{
mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "750%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/750
echo "************************************************************************************************************************">> /tmp/ets1/750

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "750%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;'>> /tmp/ets1/750
echo "************************************************************************************************************************">> /tmp/ets1/750

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "750%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/750
echo "************************************************************************************************************************">> /tmp/ets1/750

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "751%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/751
echo "************************************************************************************************************************" >> /tmp/ets1/751

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "751%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/751
echo "************************************************************************************************************************" >> /tmp/ets1/751

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "751%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/751
echo "************************************************************************************************************************" >> /tmp/ets1/751

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "752%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/752
echo "************************************************************************************************************************" >> /tmp/ets1/752

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "752%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/752
echo "************************************************************************************************************************" >> /tmp/ets1/752

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "752%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/752
echo "************************************************************************************************************************" >> /tmp/ets1/752

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "753%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/753
echo "************************************************************************************************************************">> /tmp/ets1/753

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "753%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/753
echo "************************************************************************************************************************">> /tmp/ets1/753

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "753%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/753
echo "************************************************************************************************************************">> /tmp/ets1/753

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "755%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/755
echo "************************************************************************************************************************">> /tmp/ets1/755

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "755%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/755
echo "************************************************************************************************************************">> /tmp/ets1/755

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "755%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/755
echo "************************************************************************************************************************">> /tmp/ets1/755

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "756%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/756
echo "************************************************************************************************************************">> /tmp/ets1/756

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "756%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/756
echo "************************************************************************************************************************">> /tmp/ets1/756

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "756%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/756
echo "************************************************************************************************************************">> /tmp/ets1/756

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "758%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/758
echo "************************************************************************************************************************" >> /tmp/ets1/758

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "758%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/758
echo "************************************************************************************************************************" >> /tmp/ets1/758

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "758%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/758
echo "************************************************************************************************************************" >> /tmp/ets1/758

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "759%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/759
echo "************************************************************************************************************************" >> /tmp/ets1/759

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "759%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/759
echo "************************************************************************************************************************" >> /tmp/ets1/759

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "759%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/759
echo "************************************************************************************************************************" >> /tmp/ets1/759

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "760%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/760
echo "************************************************************************************************************************" >> /tmp/ets1/760

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "760%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/760
echo "************************************************************************************************************************" >> /tmp/ets1/760

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "760%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/760
echo "************************************************************************************************************************" >> /tmp/ets1/760

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "762%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/762
echo "************************************************************************************************************************" >> /tmp/ets1/762

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "762%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/762
echo "************************************************************************************************************************" >> /tmp/ets1/762

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "762%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/762
echo "************************************************************************************************************************" >> /tmp/ets1/762

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "764%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/764
echo "************************************************************************************************************************" >> /tmp/ets1/764

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "764%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/764
echo "************************************************************************************************************************" >> /tmp/ets1/764

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "764%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/764
echo "************************************************************************************************************************" >> /tmp/ets1/764

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "765%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/765
echo "************************************************************************************************************************" >> /tmp/ets1/765

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "765%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/765
echo "************************************************************************************************************************" >> /tmp/ets1/765

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "765%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/765
echo "************************************************************************************************************************" >> /tmp/ets1/765

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "768%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/768
echo "************************************************************************************************************************" >> /tmp/ets1/768

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "768%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/768
echo "************************************************************************************************************************" >> /tmp/ets1/768

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "768%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/768
echo "************************************************************************************************************************" >> /tmp/ets1/768

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "769%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/769
echo "************************************************************************************************************************" >> /tmp/ets1/769

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "769%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/769
echo "************************************************************************************************************************" >> /tmp/ets1/769

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "769%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/769
echo "************************************************************************************************************************" >> /tmp/ets1/769

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "770%") and (disposition like "ANSWERED%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/770
echo "************************************************************************************************************************" >> /tmp/ets1/770

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "770%") and (disposition like "NO ANSWER%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/770
echo "************************************************************************************************************************" >> /tmp/ets1/770

mysql -u root --password=****** asteriskcdrdb -e 'select src,dst,disposition,calldate,duration from cdr where (dst  like "770%") and (disposition like "BUSY%") and calldate >= curdate() - INTERVAL DAYOFWEEK(curdate())+1 DAY and calldate<=curdate() ;' >> /tmp/ets1/770
echo "************************************************************************************************************************" >> /tmp/ets1/770
}}}
== How to Generate weekly report for incoming and outgoing call ==
{{{
#run this command 
#ets1-call-report
#that is command contain
rm -rf /tmp/*
outgoing-only-customer
outgoing-only-tech
incoming-only-tech
incoming-only-customer
}}}

== cron to delete voice message older than two days ==
{{{
22 8 * * *      find    /var/spool/asterisk/voicemail/default/119/INBOX/ -mtime +2 -exec rm -rf {} \;
5 8 * * *      find     /var/spool/asterisk/voicemail/default/126/INBOX/ -mtime +2 -exec rm -rf {} \;
10 8 * * *      find    /var/spool/asterisk/voicemail/default/128/INBOX/ -mtime +2 -exec rm -rf {} \;
15 8 * * *      find    /var/spool/asterisk/monitor/ -mtime +7 -exec rm -rf {} \;
}}}
