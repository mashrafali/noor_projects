{{{
Author       : Ashraf Ali
Version      : 1.0
Creation date: 2013-11-03
Latest update: 2013-11-04
}}}
<<TableOfContents(3)>>

= CBPolicyd =
== What is CBPolicyd ? ==
'''Policyd'''

Policyd v2 (codenamed "cluebringer") is a multi-platform policy server for popular MTAs. This policy daemon is designed mostly for large scale mail hosting environments. The main goal is to implement as many spam combating and email compliance features as possible while at the same time maintaining the portability, stability and performance required for mission critical email hosting of today. Most of the ideas and methods implemented in Policyd v2 stem from Policyd v1 as well as the authors' long time involvement in large scale mail hosting industry.

'''Old Policyd v1'''

Policyd v1 is an anti-spam plugin for Postfix (written in C) that does Greylisting, Sender-(envelope, SASL or host/ip)-based throttling (on messages and/or volume per defined time unit), Recipient rate limiting, Spamtrap monitoring/blacklisting, HELO auto blacklisting and HELO randomization prevention. Policyd v1 is only open for bug & security fixes, everyone is strongly advised to migrate to v2.

== Enabling and configuring CBPolicyd in Zimbra 8.0.0 ==
• Switch to the zimbra user

{{{su - zimbra}}}

• Enable the cbpolicyd service (server-wide) via zmprov:

{{{zmprov ms servername +zimbraServiceInstalled cbpolicyd +zimbraServiceEnabled cbpolicyd}}}

(mind that "+" in front of "zimbraServiceEnabled cbpolicyd". Miss it and you'll end up having cbpolicyd as the ONLY service enabled)

• In most recent Zimbra versions a CBpolicyD zimbraMtaRestriction exists. If not, add a new one:

{{{zmprov mcf +zimbraMtaRestriction "check_policy_service inet:127.0.0.1:10031"}}}

• Configure the CBPolicyd logging and active modules

{{{zmlocalconfig -e cbpolicyd_log_level=4; zmlocalconfig -e cbpolicyd_log_detail=modules,tracking,policies; zmlocalconfig -e cbpolicyd_module_accesscontrol=1 cbpolicyd_module_checkhelo=1 cbpolicyd_module_checkspf=1 cbpolicyd_module_greylisting}}}

(sets loglevel to Debug, detailed logging of Modules, Policies and Tracking, enables all the modules excluding Amavis)

• Restart the MTA service to apply all the configurations

{{{zmmtactl restart}}}

==  Activating the WEB UI for CBPolicyd ==
