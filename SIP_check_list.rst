{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2013-11-21
Latest update: 2013-11-21
}}}

<<TableOfContents>>

== SIP Server Check List ==
=== Check List Before ===
 * Before modifying the configuration on SIP Server (Add Ext.,delete Ext.,.....) you should do the below step

||<tablewidth="949px" tableheight="420px">'''Step''' ||'''Example''' ||'''Action''' ||'''Author''' ||'''Date''' ||
||Make outgoing call ||Call your mobile number or your home number. || || || ||
||Make incoming call ||Call our short number from your cell phone then follow up the IVR messages. || || || ||
||Make an internal call ||Call your colleague extension || || || ||
||Make sure that the backup configuration version is recent ||elastixbackup-20131121115437-v4.tar || || || ||
||Make sure that your can restore the configuration file either through http or CLI || || || || ||
||Do the modification after or before normal working hour except for urgent cases || || || || ||
||make sure that one of the SIP expert ||Example: inform Ashraf Ali is aware of you are doing || || || ||
||Make sure that your don't use the hunt group number or queue number as an extension ||because such action make the server hang || || || ||


=== Check List After ===
 * After  doing the modification your should perform the below checklist and if any failed restore the backup configuration or call for a help

||<tablewidth="947px" tableheight="175px">'''Step''' ||'''Example''' ||'''Action''' ||'''Author''' ||'''Date''' ||
||Make outgoing call ||Call your mobile number or your home number || || || ||
||Make incoming call ||Call our short number from your cell phone then follow up the IVR messages || || || ||
||Make an internal call ||call your colleague extension || || || ||


 * {{{
Please follow the above rule to maintain our SIP server stability .
}}}
