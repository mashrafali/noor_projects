## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Zimbra/TipsAndTricks/Remove_wiki_account
{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2014-02-19
Latest update: 2014-02-19
}}}
'''Find wiki username: '''

http://wiki.noor.net/users

'''Search for user id:'''

{{{
root@wiki:~# find /usr/local/share/moin/data/user -type f -exec grep -l "user@noor.net" {} \;
/usr/local/share/moin/data/user/1336652126.23.21770
}}}
'''Remove user id:'''

{{{
root@wiki:~# rm -rf /usr/local/share/moin/data/user/1336652126.23.21770
}}}
'''Check wiki users page : '''

http://wiki.noor.net/users
