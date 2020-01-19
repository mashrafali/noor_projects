{{{
Author       : Ashraf Ali
Version      : 1.0
Creation date: 2014-10-21
Latest update: 2014-10-21
}}}
<<TableOfContents(3)>>

== 4.7.1 Service unavailable - try again later ==

The milter_default_action parameter specifies how Postfix handles Milter application errors. The default action is to respond with a temporary error status, so that the client will try again later. Specify "accept" if you want to receive mail as if the filter does not exist, and "reject" to reject mail with a permanent status.

To prevent '''4.7.1 Service unavailable - try again later''', apply the following:

{{{
su zimbra
postconf -e milter_default_action=accept
zmconfigdctl restart
zmmilterctl restart
zmmtactl restart
}}}
