{{{
Author       : Ayman Tohamy
Version      : 1.0
Title        : Protecting Hsphere Using the IP Tables & Windows Firewall
Creation date: 2015-02-15
Latest update: 2015-02-15
}}}
 . <<TableOfContents(4)>>

== Control Panel ==

=== CP required ports ===
{{{
######################### Control Panel cp.noor.com 41.187.100.5 ############################
# Allow Control Panel Ports 
# HTTP(8080) from ALL
# DNS (53) from ALL
# Tomcat (8009) for CP only
# Postgrees(5342) for CP only
# RSYNC(873) between Hsphere servers
# SOAP(10125) between Hsphere servers

}}}

=== CP IPtables Rules ===
{{{
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
iptables -A INPUT -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -p udp --dport 53 -j ACCEPT
iptables -A INPUT -s 41.187.100.5 -p tcp --dport 8009 -j ACCEPT
iptables -A INPUT -s 41.187.100.5 -p tcp --dport 5432 -j ACCEPT
iptables -A INPUT -s 41.187.100.0/27 -p tcp --dport 873 -j ACCEPT
iptables -A INPUT -s 41.187.100.0/27 -p tcp --dport 10125 -j ACCEPT
}}}
{{{
# Allow Hsphere servers [ CP  and other hsphere servers(winweb - mail - winweb2 - web2 - mail3 )]
iptables -I INPUT 1 -i lo -j ACCEPT
iptables -A INPUT -s 41.187.100.0/27 -j ACCEPT

# Allow SSH from NOOR Premises (city stars)
iptables -A INPUT -s 217.139.224.0/24 -p tcp --dport 22 -j ACCEPT

# Allow SNMP from NOOR Zenoss (city stars)
iptables -A INPUT -s 217.139.224.113 -p tcp --dport 161 -j ACCEPT
iptables -A INPUT -s 217.139.224.113 -p udp --dport 161 -j ACCEPT

# Allow PING
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT

#Accept incoming traffic corresponding to established outgoing sessions. 
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Assign a rule to drop any other incoming connections. 
iptables -A INPUT -j DROP

}}}

=== Save & Check IP Tables Rules ===
{{{
# check firewall status then enable and save rules

/etc/init.d/iptables status
# To enable iptables
chkconfig iptables on
# Save Iptables
service iptables start
/sbin/service iptables save
}}}

{{{
# List IP Tables rules

# iptables -L -n -v --line-numbers
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1    8529K 1738M ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
2     267K   27M ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:8080 
3    25067 1477K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:53 
4      42M 2810M ACCEPT     udp  --  *      *       0.0.0.0/0            0.0.0.0/0           udp dpt:53 
5        0     0 ACCEPT     tcp  --  *      *       41.187.100.0/27      0.0.0.0/0           tcp dpt:873 
6        0     0 ACCEPT     tcp  --  *      *       41.187.100.0/27      0.0.0.0/0           tcp dpt:10125 
7        3   164 ACCEPT     tcp  --  *      *       41.187.100.5         0.0.0.0/0           tcp dpt:8009 
8        0     0 ACCEPT     tcp  --  *      *       41.187.100.5         0.0.0.0/0           tcp dpt:5432 
9    1397K  326M ACCEPT     all  --  *      *       41.187.100.0/27      0.0.0.0/0           
10      10   600 ACCEPT     tcp  --  *      *       217.139.224.113      0.0.0.0/0           tcp dpt:161 
11    4228  365K ACCEPT     udp  --  *      *       217.139.224.113      0.0.0.0/0           udp dpt:161 
12    7912  513K ACCEPT     tcp  --  *      *       217.139.224.0/24     0.0.0.0/0           tcp dpt:22 
13    244K   20M ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           icmp type 8 
14     14M   13G ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
15   1022K  218M DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0           

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 8672K packets, 1770M bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1      62M   40G ACCEPT     all  --  *      eth0    0.0.0.0/0            0.0.0.0/0                
}}}


== Mail qmail/NS server ==

=== Mail required ports ===
{{{
######################### Mail mail.noor.com 41.187.100.8 ############################
# Allow Mail Ports (MAIL + NS )

# HTTP(80) from ALL
# DNS (53) udp
# DNS (53) tcp
# SMTP (25) from ALL
# POP3 (110) for All
# IMAP (143) for All
# IMAP Proxy (144) localhost
# Submission (587) for All
# RSYNC(873) between Hsphere servers

}}}

=== Mail IPtables Rules ===
{{{
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p udp --dport 53 -j ACCEPT
iptables -A INPUT -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -p tcp --dport 25 -j ACCEPT
iptables -A INPUT -p tcp --dport 110 -j ACCEPT
iptables -A INPUT -p tcp --dport 143 -j ACCEPT
iptables -A INPUT -s 41.187.100.8 -p tcp --dport 144 -j ACCEPT
iptables -A INPUT -p tcp --dport 587 -j ACCEPT
iptables -A INPUT -s 41.187.100.0/27 -p tcp --dport 873 -j ACCEPT
}}}
{{{
# Allow Hsphere servers [ CP  and other hsphere servers(winweb - mail - winweb2 - web2 - mail3 )]
iptables -I INPUT 1 -i lo -j ACCEPT
iptables -A INPUT -s 41.187.100.0/27 -j ACCEPT

# Allow SSH from NOOR Premises (city stars)
iptables -A INPUT -s 217.139.224.0/24 -p tcp --dport 22 -j ACCEPT

# Allow SNMP from NOOR Zenoss (city stars)
iptables -A INPUT -s 217.139.224.113 -p tcp --dport 161 -j ACCEPT
iptables -A INPUT -s 217.139.224.113 -p udp --dport 161 -j ACCEPT

# Allow PING
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT

#Accept incoming traffic corresponding to established outgoing sessions. 
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Assign a rule to drop any other incoming connections. 
iptables -A INPUT -j DROP

}}}

=== Save & Check IP Tables Rules ===
{{{
# check firewall status then enable and save rules

/etc/init.d/iptables status
# To enable iptables
chkconfig iptables on
# Save Iptables
service iptables start
/sbin/service iptables save
}}}

{{{

[root@mail ~]# iptables -L -n -v --line-numbers
Chain INPUT (policy ACCEPT 40810 packets, 8388K bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1     2166  655K ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
2       81 12737 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:80 
3      866 58800 ACCEPT     udp  --  *      *       0.0.0.0/0            0.0.0.0/0           udp dpt:53 
4        0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:53 
5    11804   17M ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:25 
6    41036 1790K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:110 
7      380 26296 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:143 
8        0     0 ACCEPT     tcp  --  *      *       41.187.100.8         0.0.0.0/0           tcp dpt:144 
9     3708 4822K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:587 
10       0     0 ACCEPT     tcp  --  *      *       41.187.100.0/27      0.0.0.0/0           tcp dpt:873 
11       5   476 ACCEPT     all  --  *      *       41.187.100.0/27      0.0.0.0/0           
12     142  9532 ACCEPT     tcp  --  *      *       217.139.224.0/24     0.0.0.0/0           tcp dpt:22 
13       0     0 ACCEPT     tcp  --  *      *       217.139.224.113      0.0.0.0/0           tcp dpt:161 
14      69  7041 ACCEPT     udp  --  *      *       217.139.224.113      0.0.0.0/0           udp dpt:161 
15       3   171 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           icmp type 8 
16    1339 88761 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
17       0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0           

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 67098 packets, 180M bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        3   171 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           icmp type 0 
            
}}}


== Mail3 qmail server ==

=== Mail3 required ports ===
{{{
######################### Mail3 mail3.noor.com 41.187.100.12 ############################
# Allow Mail3 Ports (MAIL)

# HTTP(80) from ALL
# DNS (53) udp
# SMTP (25) from ALL
# POP3 (110) for All
# IMAP (143) for All
# IMAP Proxy (144) localhost
# Submission (587) for All
# RSYNC(873) between Hsphere servers

}}}

=== Mail3 IPtables Rules ===
{{{
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p udp --dport 53 -j ACCEPT
iptables -A INPUT -p tcp --dport 25 -j ACCEPT
iptables -A INPUT -p tcp --dport 110 -j ACCEPT
iptables -A INPUT -p tcp --dport 143 -j ACCEPT
iptables -A INPUT -s 41.187.100.12 -p tcp --dport 144 -j ACCEPT
iptables -A INPUT -p tcp --dport 587 -j ACCEPT
iptables -A INPUT -s 41.187.100.0/27 -p tcp --dport 873 -j ACCEPT
}}}
{{{
# Allow Hsphere servers [ CP  and other hsphere servers(winweb - mail - winweb2 - web2 - mail3 )]
iptables -I INPUT 1 -i lo -j ACCEPT
iptables -A INPUT -s 41.187.100.0/27 -j ACCEPT

# Allow SSH from NOOR Premises (city stars)
iptables -A INPUT -s 217.139.224.0/24 -p tcp --dport 22 -j ACCEPT

# Allow SNMP from NOOR Zenoss (city stars)
iptables -A INPUT -s 217.139.224.113 -p tcp --dport 161 -j ACCEPT
iptables -A INPUT -s 217.139.224.113 -p udp --dport 161 -j ACCEPT

# Allow PING
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT

#Accept incoming traffic corresponding to established outgoing sessions. 
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Assign a rule to drop any other incoming connections. 
iptables -A INPUT -j DROP

}}}

=== Save & Check IP Tables Rules ===
{{{
# check firewall status then enable and save rules

/etc/init.d/iptables status
# To enable iptables
chkconfig iptables on
# Save Iptables
service iptables start
/sbin/service iptables save
}}}

{{{
[root@mail3 ~]# iptables -L -n -v --line-numbers
Chain INPUT (policy ACCEPT 7710 packets, 876K bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1      481 65720 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
2      566 85069 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:80 
3        0     0 ACCEPT     udp  --  *      *       0.0.0.0/0            0.0.0.0/0           udp dpt:53 
4    17821   24M ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:25 
5    14645  680K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:110 
6     1458 93983 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:143 
7        0     0 ACCEPT     tcp  --  *      *       41.187.100.12        0.0.0.0/0           tcp dpt:144 
8     3188 4244K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           tcp dpt:587 
9        0     0 ACCEPT     tcp  --  *      *       41.187.100.0/27      0.0.0.0/0           tcp dpt:873 
10    1708 94031 ACCEPT     all  --  *      *       41.187.100.0/27      0.0.0.0/0           
11     581 42924 ACCEPT     tcp  --  *      *       217.139.224.0/24     0.0.0.0/0           tcp dpt:22 
12       0     0 ACCEPT     tcp  --  *      *       217.139.224.113      0.0.0.0/0           tcp dpt:161 
13     241 25997 ACCEPT     udp  --  *      *       217.139.224.113      0.0.0.0/0           udp dpt:161 
14      64  5274 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           icmp type 8 
15    6443  460K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
16    2208  120K DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0           

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 35111 packets, 47M bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1       64  5274 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           icmp type 0              
}}}
