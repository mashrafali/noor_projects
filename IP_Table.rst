## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/IP_Table
## page was renamed from Enterprise/IT/Docs/HowTo/Linux/IP_Table
## page was renamed from Enterprise/IT/Docs/How_To/Linux/IP_Table
{{{
Author       : Ayman Tohamy
Version      : 1.0
Creation date: 2012-07-12
Latest update: 2012-07-12
}}}
<<TableOfContents(3)>>

= IPTables =
== 1. Introduction ==
CentOS  has an extremely powerful firewall built in, commonly referred to as  iptables, but more accurately is iptables/netfilter. Iptables is the  userspace module, the bit that you, the user, interact with at the  command line to enter firewall rules into predefined tables. Netfilter  is a kernel module, built into the kernel, that actually does the  filtering. There are many GUI front ends for iptables that allow users  to add or define rules based on a point and click user interface, but  these often lack the flexibility of using the command line interface and  limit the users understanding of what's really happening. We're going  to learn the command line interface of iptables.

Before  we can really get to grips with iptables, we need to have at least a  basic understanding of the way it works. Iptables uses the concept of IP  addresses, protocols (tcp, udp, icmp) and ports. We don't need to be  experts in these to get started (as we can look up any of the  information we need), but it helps to have a general understanding.

Iptables  places rules into predefined chains (INPUT, OUTPUT and FORWARD) that  are checked against any network traffic (IP packets) relevant to those  chains and a decision is made about what to do with each packet based  upon the outcome of those rules, i.e. accepting or dropping the packet.  These actions are referred to as targets, of which the two most common  predefined targets are DROP to drop a packet or ACCEPT to accept a  packet.

'''Chains'''

These  are 3 predefined chains in the filter table to which we can add rules  for processing IP packets passing through those chains. These chains  are:

 * INPUT - All packets destined for the host computer.
 * OUTPUT - All packets originating from the host computer.
 * FORWARD  - All packets neither destined for nor originating from the host  computer, but passing through (routed by) the host computer. This chain  is used if you are using your computer as a router.

For  the most part, we are going to be dealing with the INPUT chain to  filter packets entering our machine - that is, keeping the bad guys out.

Rules  are added in a list to each chain. A packet is checked against each  rule in turn, starting at the top, and if it matches that rule, then an  action is taken such as accepting (ACCEPT) or dropping (DROP) the  packet. Once a rule has been matched and an action taken, then the  packet is processed according to the outcome of that rule and isn't  processed by further rules in the chain. If a packet passes down through  all the rules in the chain and reaches the bottom without being matched  against any rule, then the default action for that chain is taken. This  is referred to as the default policy and may be set to either ACCEPT or  DROP the packet.

'''The  concept of default policies within chains raises two fundamental  possibilities that we must first consider before we decide how we are  going to organize our firewall.'''

''' '''1.  We can set a default policy to DROP all packets and then add rules to  specifically allow (ACCEPT) packets that may be from trusted IP  addresses, or for certain ports on which we have services running such  as bittorrent, FTP server, Web Server, Samba file server etc.

or alternatively,

2.  We can set a default policy to ACCEPT all packets and then add rules to  specifically block (DROP) packets that may be from specific nuisance IP  addresses or ranges, or for certain ports on which we have private  services or no services running.

Generally,  option 1 above is used for the INPUT chain where we want to control  what is allowed to access our machine and option 2 would be used for the  OUTPUT chain where we generally trust the traffic that is leaving  (originating from) our machine.

== 2. Getting Started ==
Working  with iptables from the command line requires root privileges, so you  will need to become root for most things we will be doing.
||<class="lmimg orange" style="padding:0;text-align:center;vertical-align:top">  ||||<class="lmtxt orange" style="padding:0;text-align:left">'''IMPORTANT:'''  We will be turning off iptables and resetting your firewall rules, so  if you are reliant on your Linux firewall as your primary line of  defense you should be aware of this. ||




Iptables  should be installed by default on all CentOS 5.x and 6.x installations.  You can check to see if iptables is installed on your system by:

{{{
$ rpm -q iptables
iptables-1.4.7-5.1.el6_2.x86_64
}}}
And  to see if iptables is actually running, we can check that the iptables  modules are loaded and use the -L switch to inspect the currently loaded  rules:

{{{
# lsmod | grep ip_tables
ip_tables              29288  1 iptable_filter
x_tables               29192  6 ip6t_REJECT,ip6_tables,ipt_REJECT,xt_state,xt_tcpudp,ip_tables
}}}
{{{
# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere            state RELATED,ESTABLISHED
ACCEPT     icmp --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere            state NEW tcp dpt:ssh
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
}}}
Above we see the default set of rules on a CentOS 6 system. Note that SSH service is permitted by default.

If iptables is not running, you can enable it by running:

{{{
# system-config-securitylevel
}}}
== 3. Writing a Simple Rule Set ==
||<class="lmimg orange" style="padding:0;text-align:center;vertical-align:top">  ||||<class="lmtxt orange" style="padding:0;text-align:left">'''IMPORTANT:'''  At this point we are going to clear the default rule set. If you are  connecting remotely to a server via SSH for this tutorial then there is a  very real possibility that you could lock yourself out of your machine.  You '''must''' set the default input policy to accept before  flushing the current rules, and then add a rule at the start to  explicitly allow yourself access to prevent against locking yourself  out. ||


We  will use an example based approach to examine the various iptables  commands. In this first example, we will create a very simple set of  rules to set up a Stateful Packet Inspection (SPI) firewall that will  allow all outgoing connections but block all unwanted incoming  connections:

{{{
# iptables -P INPUT ACCEPT
# iptables -F
# iptables -A INPUT -i lo -j ACCEPT
# iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
# iptables -A INPUT -p tcp --dport 22 -j ACCEPT
# iptables -P INPUT DROP
# iptables -P FORWARD DROP
# iptables -P OUTPUT ACCEPT
# iptables -L -v
}}}
which should give the following output:

{{{
Chain INPUT (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  lo     any     anywhere             anywhere
    0     0 ACCEPT     all  --  any    any     anywhere             anywhere            state RELATED,ESTABLISHED
    0     0 ACCEPT     tcp  --  any    any     anywhere             anywhere            tcp dpt:ssh
Chain FORWARD (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
}}}
Now lets look at each of the 8 commands above in turn and understand exactly what we've just done:

 1. '''iptables -P INPUT ACCEPT'''   If connecting remotely we must first temporarily set the default  policy on the INPUT chain to ACCEPT otherwise once we flush the current  rules we will be locked out of our server.
 1. '''iptables -F'''  We used the -F switch to flush all existing rules so we start with a clean state from which to add new rules.
 1. ''' iptables -A INPUT -i lo -j ACCEPT'''   Now it's time to start adding some rules. We use the -A switch to  append (or add) a rule to a specific chain, the INPUT chain in this  instance. Then we use the -i switch (for interface) to specify packets  matching or destined for the lo (localhost, 127.0.0.1) interface and  finally -j (jump) to the target action for packets matching the rule -  in this case ACCEPT. So this rule will allow all incoming packets  destined for the localhost interface to be accepted. This is generally  required as many software applications expect to be able to communicate  with the localhost adaptor.
 1. '''iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT'''   This is the rule that does most of the work, and again we are adding  (-A) it to the INPUT chain. Here we're using the -m switch to load a  module (state). The state module is able to examine the state of a  packet and determine if it is NEW, ESTABLISHED or RELATED. NEW refers to  incoming packets that are new incoming connections that weren't  initiated by the host system. ESTABLISHED and RELATED refers to incoming  packets that are part of an already established connection or related  to and already established connection.
 1. '''iptables -A INPUT -p tcp --dport 22 -j ACCEPT'''   Here we add a rule allowing SSH connections over tcp port 22. This is  to prevent accidental lockouts when working on remote systems over an  SSH connection. We will explain this rule in more detail later.
 1. ''' iptables -P INPUT DROP'''   The -P switch sets the default policy on the specified chain. So now  we can set the default policy on the INPUT chain to DROP. This means  that if an incoming packet does not match one of the following rules it  will be dropped. If we were connecting remotely via SSH and had not  added the rule above, we would have just locked ourself out of the  system at this point.
 1. '''iptables -P FORWARD DROP'''   Similarly, here we've set the default policy on the FORWARD chain to  DROP as we're not using our computer as a router so there should not be  any packets passing through our computer.
 1. '''iptables -P OUTPUT ACCEPT'''   and finally, we've set the default policy on the OUTPUT chain to  ACCEPT as we want to allow all outgoing traffic (as we trust our users).
 1. '''iptables -L -v'''  Finally, we can list (-L) the rules we've just added to check they've been loaded correctly.

Finally,  the last thing we need to do is save our rules so that next time we  reboot our computer our rules are automatically reloaded:

{{{
# /sbin/service iptables save
}}}
This  executes the iptables init script, which runs /sbin/iptables-save and  writes the current iptables configuration to /etc/sysconfig/iptables.  Upon reboot, the iptables init script reapplies the rules saved in  /etc/sysconfig/iptables by using the /sbin/iptables-restore command.

Obviously  typing all these commands at the shell can become tedious, so by far  the easiest way to work with iptables is to create a simple script to do  it all for you. The above commands may be entered into your favourite  text editor and saved as myfirewall, for example:

{{{#!/bin/bash # # iptables example configuration script # # Flush all current rules from iptables #

 . iptables -F

# # Allow SSH connections on tcp port 22 # This is essential when working on remote servers via SSH to prevent locking yourself out of the system #

 . iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# # Set default policies for INPUT, FORWARD and OUTPUT chains #

 . iptables -P INPUT DROP iptables -P FORWARD DROP iptables -P OUTPUT ACCEPT

# # Set access for localhost #

 . iptables -A INPUT -i lo -j ACCEPT

# # Accept packets belonging to established and related connections #

 . iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# # Save settings #

 . /sbin/service iptables save

# # List rules #

 . iptables -L -v

}}} '''Note:''' We can also comment our script to remind us what were doing.

now make the script executable:

{{{
# chmod +x myfirewall
}}}
We can now simply edit our script and run it from the shell with the following command:

{{{
# ./myfirewall
}}}
== 4. Interfaces ==
In  our previous example, we saw how we could accept all packets incoming  on a particular interface, in this case the localhost interface:

{{{
iptables -A INPUT -i lo -j ACCEPT
}}}
Suppose  we have 2 separate interfaces, eth0 which is our internal LAN  connection and ppp0 dialup modem (or maybe eth1 for a nic) which is our  external internet connection. We may want to allow all incoming packets  on our internal LAN but still filter incoming packets on our external  internet connection. We could do this as follows:

{{{
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i eth0 -j ACCEPT
}}}
'''But be very careful''' - if we were to allow all packets for our external internet interface (for example, ppp0 dialup modem):

{{{
iptables -A INPUT -i ppp0 -j ACCEPT
}}}
'''we would have effectively just disabled our firewall!'''

== 5. IP Addresses ==
Opening  up a whole interface to incoming packets may not be restrictive enough  and you may want more control as to what to allow and what to reject.  Lets suppose we have a small network of computers that use the  192.168.0.x private subnet. We can open up our firewall to incoming  packets from a single trusted IP address (for example, 192.168.0.4):

{{{
# Accept packets from trusted IP addresses
 iptables -A INPUT -s 192.168.0.4 -j ACCEPT # change the IP address as appropriate
}}}
Breaking  this command down, we first append (-A) a rule to the INPUT chain for  the source (-s) IP address 192.168.0.4 to ACCEPT all packets (also note  how we can use the # symbol to add comments inline to document our  script with anything after the # being ignored and treated as a  comment).

Obviously  if we want to allow incoming packets from a range of IP addresses, we  could simply add a rule for each trusted IP address and that would work  fine. But if we have a lot of them, it may be easier to add a range of  IP addresses in one go. To do this, we can use a netmask or standard  slash notation to specify a range of IP address. For example, if we  wanted to open our firewall to all incoming packets from the complete  192.168.0.x (where x=1 to 254) range, we could use either of the  following methods:

{{{
# Accept packets from trusted IP addresses
 iptables -A INPUT -s 192.168.0.0/24 -j ACCEPT  # using standard slash notation
 iptables -A INPUT -s 192.168.0.0/255.255.255.0 -j ACCEPT # using a subnet mask
}}}
Finally,  as well as filtering against a single IP address, we can also match  against the MAC address for the given device. To do this, we need to  load a module (the mac module) that allows filtering against mac  addresses. Earlier we saw another example of using modules to extend the  functionality of iptables when we used the state module to match for  ESTABLISHED and RELATED packets. Here we use the mac module to check the  mac address of the source of the packet in addition to it's IP address:

{{{
# Accept packets from trusted IP addresses
 iptables -A INPUT -s 192.168.0.4 -m mac --mac-source 00:50:8D:FD:E6:32 -j ACCEPT
}}}
First we use `-m mac` to load the mac module and then we use `--mac-source`  to specify the mac address of the source IP address (192.168.0.4). You  will need to find out the mac address of each ethernet device you wish  to filter against. Running `ifconfig` (or `iwconfig` for wireless devices) as root will provide you with the mac address.

This  may be useful for preventing spoofing of the source IP address as it  will allow any packets that genuinely originate from 192.168.0.4 (having  the mac address 00:50:8D:FD:E6:32) but will block any packets that are  spoofed to have come from that address. Note, mac address filtering  won't work across the internet but it certainly works fine on a LAN.

== 6. Ports and Protocols ==
Above  we have seen how we can add rules to our firewall to filter against  packets matching a particular interface or a source IP address. This  allows full access through our firewall to certain trusted sources (host  PCs). Now we'll look at how we can filter against protocols and ports  to further refine what incoming packets we allow and what we block.

Before  we can begin, we need to know what protocol and port number a given  service uses. For a simple example, lets look at bittorrent. Bittorrent  uses the tcp protocol on port 6881, so we would need to allow all tcp  packets on destination port (the port on which they arrive at our  machine) 6881:

{{{
# Accept tcp packets on destination port 6881 (bittorrent)
 iptables -A INPUT -p tcp --dport 6881 -j ACCEPT
}}}
Here we append (-A) a rule to the INPUT chain for packets matching the tcp protocol (`-p tcp`) and entering our machine on destination port 6881 (`--dport 6881`).

'''Note:''' In order to use matches such as destination or source ports (`--dport` or `--sport`), you''' must''' first specify the protocol (tcp, udp, icmp, all).

We can also extend the above to include a port range, for example, allowing all tcp packets on the range 6881 to 6890:

{{{
# Accept tcp packets on destination ports 6881-6890
 iptables -A INPUT -p tcp --dport 6881:6890 -j ACCEPT
}}}
== 7. Putting It All Together ==
Now we've seen the basics, we can start combining these rules.

A  popular UNIX/Linux service is the secure shell (SSH) service allowing  remote logins. By default SSH uses port 22 and again uses the tcp  protocol. So if we want to allow remote logins, we would need to allow  tcp connections on port 22:

{{{
# Accept tcp packets on destination port 22 (SSH)
 iptables -A INPUT -p tcp --dport 22 -j ACCEPT
}}}
This  will open up port 22 (SSH) to all incoming tcp connections which poses a  potential security threat as hackers could try brute force cracking on  accounts with weak passwords. However, if we know the IP addresses of  trusted remote machines that will be used to log on using SSH, we can  limit access to only these source IP addresses. For example, if we just  wanted to open up SSH access on our private lan (192.168.0.x), we can  limit access to just this source IP address range:

{{{
# Accept tcp packets on destination port 22 (SSH) from private LAN
 iptables -A INPUT -p tcp -s 192.168.0.0/24 --dport 22 -j ACCEPT
}}}
Using  source IP filtering allows us to securely open up SSH access on port 22  to only trusted IP addresses. For example, we could use this method to  allow remote logins between work and home machines. To all other IP  addresses, the port (and service) would appear closed as if the service  were disabled so hackers using port scanning methods are likely to pass  us by.

== 8. Summary ==
We've  barely scratched the surface of what can be achieved with iptables, but  hopefully this HOWTO has provided a good grounding in the basics from  which one may build more complicated rule sets.

== 9. Links ==
http://www.centos.org/docs/5/html/Deployment_Guide-en-US/ch-fw.html

http://www.centos.org/docs/5/html/Deployment_Guide-en-US/ch-iptables.html
