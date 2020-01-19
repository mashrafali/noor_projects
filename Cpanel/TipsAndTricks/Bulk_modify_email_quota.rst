{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Bulk Modify Email accounts quota.
Creation date: 2017-01-16
Latest update: 2017-01-16
}}}
 . <<TableOfContents(4)>>

1- Export All 

 {{{
# cat /etc/userdomains | sed "s/://g" | awk {'system("ls -1d /home/"$2"/mail/"$1"/* 2> /dev/null")'} | sed "s/\// /g" | awk {'print $5"@"$4'} > /tmp/all
}}}


2- To Modify single Email Address

 {{{
# uapi --user=cPANEL_USERNAME Email edit_pop_quota email=EMAIL_ADDRESS_USER quota=250 domain=USER_DOMAIN
}}}

 {{{
# uapi --user=elsewedy Email edit_pop_quota email=test quota=350 domain=elsewedyindustries.com
}}}
       
 {{{
apiversion: 3
func: edit_pop_quota
module: Email
result: 
  data: ~
  errors: ~
  messages: ~
  metadata: {}

  status: 1
}}}

3- To get Mailbox Quota size
 
A- Get Mailboxes quota (Byte)

 {{{
uapi --user=elsewedy Email get_pop_quota email=test domain=elsewedyindustries.com as_bytes=1
}}}

B- Get Mailboxes quota (MB)
 {{{
# uapi --user=elsewedy Email get_pop_quota email=test domain=elsewedyindustries.com as_bytes=0
--- 
apiversion: 3
func: get_pop_quota
module: Email
result: 
  data: '350.00'
  errors: ~
  messages: ~
  metadata: {}

  status: 1

}}}

4- To Export list of email addresses with its quota

 {{{
# for i in '/tmp/all-indust '; do  uapi --user=elsewedy Email get_pop_quota email="$i" domain=elsewedyindustries.com as_bytes=0;done
}}}

 {{{

# for i in `cat /tmp/all-indust`; do echo "$i-`uapi --user=elsewedy Email get_pop_quota email="$i" domain=elsewedyindustries.com as_bytes=0 | grep "data: [',u]"`"  >> /tmp/all-inst-disk ;done

}}}

5- To Bulk Modify email addresses quota


Hint: use below command to convert windows excel space encode to linux

 {{{
# cat  /tmp/cement-disk2.csv | tr -s [:space:] '\n' >/tmp/cement-unlimited
}}}

 {{{
 # cat  /tmp/ind-disk2.csv | tr -s [:space:] '\n' >/tmp/ind-unlimited
}}}

- after having list of unlited users add them to file then run loop to modify all of them:

 {{{
# for i in `cat /tmp/cement-unlimited`; do uapi --user=elsewedy Email edit_pop_quota email=$i quota=250 domain=elsewedycement.com ; done   
}}}

 {{{

# for i in `cat /tmp/ind-unlimited`; do uapi --user=elsewedy Email edit_pop_quota email=$i quota=250 domain=elsewedyindustries.com ; done 
}}}
