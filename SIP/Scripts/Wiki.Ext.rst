{{{
Author       : Mahmoud Hamdy 
Version      : 1.0
Creation date: 12-06-2016
Topic        : How to sort IP phone extensions in wiki from SIP server 
}}}

<<TableOfContents(3)>>

== How to sort IP phone extensions in wiki from SIP server ==

{{{
team=$1
grep -i $team /home/mhamdy/Desktop/Ext | sort | sed 's/^/||/' | sed 's/-//' |sed "s/$team/||/" | sed 's/<//' | sed 's/>/||/'
}}}

1. Take the above script and put it on a linux machine and make it excutable

2. go to web page of sip server and copy all the extensions and put them in a certain path and change the path in the script


the file of the sip server will be like that

{{{
CTS2 <124>
MNOC <125>
Voicemail-NOC <126>
Voicemail-Customer-Service <128>
Voicemail - ADSLSales <129>
Alex branch <130>
Youmna Adel - SDM <131>
Patrick Micheal - SDM <132>
Omar ElShazly - SDM <133>
}}}

But after excuting the script it will be like that

{{{
mhamdy@mhamdy:~$ elastix ETS1

||AbdElRahman Ahmed   ||  751||
||Ahmed Rady   ||  758||
||Ahmed Shehata   ||  760||
||Amr Essam   ||  755||
||Aya Osama   ||  762||
||Heba Mohamed   ||  761||
||Heba Othman   ||  753||
||Khaled Hussein   ||  752||
||Mark Adel   ||  757||
||Mohab Mahdy   ||  768||
||Mohamed Raouf   ||  754||
||Mohamed Salama   ||  756||
||Mohamed Wahba   ||  703||
||Ramy Atef   ||  759||
}}}

take the above output and paste it on wiki
