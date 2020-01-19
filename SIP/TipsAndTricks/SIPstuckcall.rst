## page was renamed from Enterprise/IT/Docs/TipsAndTricks/SIPstuckcall
{{{
Author       : Ahmed AbdelSattaar
Version      : 1
Creation date: 2014-03-31
Latest update: 2014-03-31
}}}
'''Stuck sip channel'''

unended Channel between two  extension which create a huge size file if the recording is enabled on any of these  extension.

'''Detect Stuck sip channel or call '''

We can list all active channel and detect stuck channel via  searching  for long duration channel or call
{{{
$asterisk -vvvvvrx 'core show channels verbose'
}}}
to display  more information about channel use command
{{{
$asterisk -vvvvvrx "core show channel SIP/804-0001e2f3"
}}}
'''Force Terminate  Stuck sip channel <<BR>>'''
{{{
$asterisk -vvvvvrx "channel request hangup SIP/804-0001e2f3"
}}}
