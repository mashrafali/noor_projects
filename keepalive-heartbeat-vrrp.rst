{{{
Author       : Mohamed Ashraf
Title        : Shared IP High availability using Heartbeats and VRRP
Version      : 1.0
Creation date: 23/11/2014
Latest update: 23/11/2014
}}}

<<TableOfContents(4)>>


{{attachment:1.png}}



= Introduction =

The open source tool that we are going to use for this feature is "Keepalived"

Keepalived implements a set of checkers to dynamically and deceptively maintain and manage high-availability that is achieved by VRRP protocol. 

In addition, Keepalived implements a set of hooks to the VRRP finite state machine providing low-level and high-speed protocol interactions.

= Keepalived Manual =

[[attachment:KeepAlived-Manual.pdf]]

= Installation =

For ubuntu/Debian Based systems:

- Install the package

{{{
apt-get update && apt-get install keepalived -y
}}}

- Configuration file is '''@/etc/keepalived/keepalived.conf'''

copy a backup of the file and edit it

{{{
cp /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.BAKUP
nano /etc/keepalived/keepalived.conf
}}}

- In our used case we used the following configuration parameters

{{{

global_defs {
             router_id haproxy1
            }
vrrp_script CHK-LB {
                      script "killall -0 haproxy"
                      interval 2
                      weight 2
                    }
vrrp_instance 50 {
                      virtual_router_id 50
                      advert_int 1
                      priority 100
                      state MASTER
                      interface eth0
                      virtual_ipaddress {
                                            192.168.0.75 dev eth0
                                        }
                      track_script {
                                    CHK-LB
                                   }
                 }


vrrp_instance 51 {
                      virtual_router_id 51
                      advert_int 1
                      priority 100
                      state MASTER
                      interface eth1
                      virtual_ipaddress {
                                            10.10.10.10 dev eth1
                                        }
                      track_script {
                                    CHK-LB
                                   }
                 }

}}}

'''WHERE:'''

vrrp_script <name> : The Health Check Script used for heartbeats, the script need to exit with code 0 for it to be considered healthy. A custom script can be created and called here

vrrp_instance <String> : This is declaration of the Floating IP Object.

this is where you define the priority - which is unique per server in the group - also configure the interface.

= Case Use =

A Typical Use for the module is as illustrated in the below topology

{{attachment:2.png}}


'''''Useful Resource :'''''

http://prefetch.net/articles/linuxkeepalivedvrrp.html
