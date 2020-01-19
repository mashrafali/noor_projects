{{{
Author       : Mohamed Ashraf
Title        : Shared IP High availability using Heartbeats and VRRP Over GRE Tunnel
Version      : 1.0
Creation date: 14/1/2015
Latest update: 14/1/2015
}}}

<<TableOfContents(4)>>


{{attachment:logo.jpg}}



= Introduction =

The main Difference between this module and the normal HA module, is that we need to Push VRRP Packets between two boxes that are in different subnets/sites.

The Packets will be pushed Over a GRE Tunnel between the two boxes.

The open source tool that we are going to use for this feature is "Keepalived"

Keepalived implements a set of checkers to dynamically and deceptively maintain and manage high-availability that is achieved by VRRP protocol. 

In addition, Keepalived implements a set of hooks to the VRRP finite state machine providing low-level and high-speed protocol interactions.

= Installation =

For ubuntu/Debian Based systems:

- Enable GRE-Module

{{{
modprobe ip_gre
echo "ip_gre" >> /etc/modules
}}}

- Activate GRE-LINK

{{{
ip tunnel add gre1 mode gre remote 192.168.0.155 local 192.168.0.153 ttl 255
ip link set gre1 up
ip addr add 10.10.10.2/24 dev gre1
}}}

Work your way to autoboot this script

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

- In our Test case we used the following configuration parameters

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
                      interface gre1
                      virtual_ipaddress {
                                            1.1.1.1 dev eth0
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


'''''Useful Resource :'''''

http://ask.xmodulo.com/create-gre-tunnel-linux.html
