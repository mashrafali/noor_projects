{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-10-29
Latest update: 2014-10-29
}}}
<<TableOfContents(3)>>

== Delete Cpanel Bulk Users ==
'''1- Create file '''



{{{
vi  /tmp/delemails.txt
}}}
{{{
k.said@elsewedyindustries.com
k.radwan@elsewedyindustries.com
m.mohamed@elsewedyindustries.com
}}}
'''2- Run below Cpanel Script to drop users'''


{{{
# for i in `cat /tmp/delemails.txt`; do /usr/local/cpanel/scripts/delpop "$i" ; done
}}}
'''Output will like this: '''

{{{

<br />Horde data for â€œm.ismail@elsewedyindustries.comâ€‌ has been removed.
<br />Deleted m.ismail@elsewedyindustries.com for user elsewedy
Account does not exist.
Account does not exist.
}}}
