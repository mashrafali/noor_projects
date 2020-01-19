{{{
Author       : Mohamed Ashraf
Title        : NOOR's FortiGate Troubleshooting Checklist
Version      : 1.0
Creation date: 16/9/2015
Latest update: 17/9/2015
}}}

<<TableOfContents(4)>>


= Introduction =

- Prior to Troubleshooting FG Connections, the L2/L3 Designs needs to be comprehended.

[[Enterprise/IT/Docs/HowTo/Linux/FortiGate-DC/L2-Design           | FortiGate UTM Layer-2 Design]]

[[Enterprise/IT/Docs/HowTo/Linux/FortiGate-DC/L3-Design           | FortiGate UTM Layer-3 Design]]


= Connections Troubleshooting Check list =

== Customer PE Connections Check ==

- Check Customer PE-CE Connection

{{{
pe.alma.ca.asr1#s int de | i Masria Cars
Gi0/0/1.119                    up             up       Masria Cars Marghany HQ Internet Link - 3M

pe.alma.ca.asr1#s ru in Gi0/0/1.119
Load for five secs: 2%/0%; one minute: 4%; five minutes: 4%
Time source is NTP, 14:52:48.666 CLT Thu Sep 17 2015

Building configuration...

Current configuration : 244 bytes
!
interface GigabitEthernet0/0/1.119
 description Masria Cars Marghany HQ Internet Link - 3M
 encapsulation dot1Q 119
 ip vrf forwarding masria-mgd
 ip address 172.16.20.1 255.255.255.252
 service-policy input 3M
 service-policy output 3M
end

pe.alma.ca.asr1#p v masria-mgd 172.16.20.2 r 980 si 1500 v df d f0f0
Type escape sequence to abort.
Sending 980, 1500-byte ICMP Echos to 172.16.20.2, timeout is 2 seconds:
Packet sent with the DF bit set
Packet has data pattern 0xF0F0
Reply data will be validated
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Success rate is 100 percent (980/980), round-trip min/avg/max = 15/17/70 ms
}}}

- From the customer's connected PE, check his connection to His FG Interface

{{{
pe.alma.ca.asr1#p v masria-mgd 172.16.30.2 r 980 si 1500 v df d f0f0
Type escape sequence to abort.
Sending 980, 1500-byte ICMP Echos to 172.16.30.2, timeout is 2 seconds:
Packet sent with the DF bit set
Packet has data pattern 0xF0F0
Reply data will be validated
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Success rate is 100 percent (980/980), round-trip min/avg/max = 1/2/9 ms
}}}

- Make Sure that the Customer Site Default route is directed to his Internet CPE not his MPLS CPE.

== MVPN Connections Check ==

- Check Customer sub interface status @MVPN ''[The traffic flows through Gi0/1/5]''

{{{
mvpn.rams.ca.asr1#s int de | i Gi0/1/5 
Gi0/1/5                        up             up       Connected to Agg-NGN Gi8/1 - Fortinet Inbound Interface
Gi0/1/5.233001                 up             up       Masria Cars-Internet Link
Gi0/1/5.233002                 up             up       Mansour Fortigate Tenant

mvpn.rams.ca.asr1#s ru in  Gi0/1/5.233001
Load for five secs: 4%/0%; one minute: 2%; five minutes: 2%
Time source is NTP, 14:42:58.955 CLT Thu Sep 17 2015

Building configuration...

Current configuration : 196 bytes
!
interface GigabitEthernet0/1/5.233001
 description Masria Cars-Internet Link
 encapsulation dot1Q 23 second-dot1q 3001
 ip vrf forwarding masria-mgd
 ip address 172.16.30.1 255.255.255.252
end

mvpn.rams.ca.asr1#p v masria-mgd 172.16.30.2 r 980 si 1500 v df d f0f0   
Type escape sequence to abort.
Sending 980, 1500-byte ICMP Echos to 172.16.30.2, timeout is 2 seconds:
Packet sent with the DF bit set
Packet has data pattern 0xF0F0
Reply data will be validated
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Success rate is 100 percent (980/980), round-trip min/avg/max = 1/1/2 ms
}}}

- Check internet connectivity under customer vrf from MVPN

{{{
mvpn.rams.ca.asr1#p v masria-mgd 8.8.8.8 r 980 si 1500 v df d f0f0 
Type escape sequence to abort.
Sending 980, 1500-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
Packet sent with the DF bit set
Packet has data pattern 0xF0F0
Reply data will be validated
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Success rate is 100 percent (980/980), round-trip min/avg/max = 56/57/84 ms
}}}

== Pe-Ramsis Connections Check ==

- Bare in Mind that PE-Ramsis Routes the FortiGate ''outbound Natted & clean'' traffic

- In PE-Ramsis there are two cases

=== Customer does not apply IPSEC ===

-> In this Case his traffic flows through a shared internet pipe with all other customers with the same case:

{{{
pe.rams.ca.asr1#s int de | i Gi1/0/1.243000
Gi1/0/1.243000                 up             up       Connected to FortiGate Outbound - Internet Access
}}}

- You can proceed to check the connection of the shared Internet Pipe.

{{{
pe.rams.ca.asr1#s ru in Gi1/0/1.243000
Load for five secs: 11%/3%; one minute: 6%; five minutes: 7%
Time source is NTP, 15:11:09.117 CLT Thu Sep 17 2015

Building configuration...

Current configuration : 191 bytes
!
interface GigabitEthernet1/0/1.243000
 description Connected to FortiGate Outbound - Internet Access
 encapsulation dot1Q 24 second-dot1q 3000
 ip address 172.29.10.77 255.255.255.252
end

pe.rams.ca.asr1#p 172.29.10.78 r 980 si 1500 v df d f0f0       
Type escape sequence to abort.
Sending 980, 1500-byte ICMP Echos to 172.29.10.78, timeout is 2 seconds:
Packet sent with the DF bit set
Packet has data pattern 0xF0F0
Reply data will be validated
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Success rate is 100 percent (980/980), round-trip min/avg/max = 1/1/1 ms
}}}

=== Customer Has an IPSEC Profile ===

-> In this case the customer has his own C-Tag QinQed Iface, so you must check his own sub interface

{{{
pe.rams.ca.asr1#s int de | i Gi1/0/1.24
Gi1/0/1.24                     deleted        down     
Gi1/0/1.243000                 up             up       Connected to FortiGate Outbound - Internet Access
Gi1/0/1.243003                 up             up       Mansour Fortigate Tenant
Gi1/0/1.243004                 up             up       Core FG IPSec Test
}}}

- Then Proceed to check his p2p connection.

{{{
pe.rams.ca.asr1#s ru in Gi1/0/1.243003
Load for five secs: 4%/1%; one minute: 6%; five minutes: 6%
Time source is NTP, 15:17:28.550 CLT Thu Sep 17 2015

Building configuration...

Current configuration : 166 bytes
!
interface GigabitEthernet1/0/1.243003
 description Mansour Fortigate Tenant
 encapsulation dot1Q 24 second-dot1q 3003
 ip address 217.139.1.89 255.255.255.252
end

pe.rams.ca.asr1#p 217.139.1.90 r 980 si 1500 v df d f0f0
Type escape sequence to abort.
Sending 980, 1500-byte ICMP Echos to 217.139.1.90, timeout is 2 seconds:
Packet sent with the DF bit set
Packet has data pattern 0xF0F0
Reply data will be validated
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Success rate is 100 percent (980/980), round-trip min/avg/max = 1/1/1 ms
}}}

=  Routing Troubleshooting Check list =

- For each Customer Provisioned, two BGP sessions are configured for him:

1 - FortiGate to MVPN BGP session to Advertise Static Route under the customer VRF.

2 - FortiGate to Pe-Ramsis BGP session to Advertize the Customer Public Subnet. ''[Note that our FortiGate Offloads Natting from MVPN, so the outbound traffic has public IPs source]''

== Customer Default Route Check ==

- Check that the default route is advertised correctly under his vrf

{{{
pe.alma.ca.asr1#s ip ro v masria-mgd | i 0.0.0.0
Gateway of last resort is 217.139.253.22 to network 0.0.0.0
B*    0.0.0.0/0 [200/0] via 217.139.253.22, 3w3d                     <-Note the BGP Code Indication
}}}

== Check FortiGate-MVPN BGP Session ==

- Check the BGP Session status initiated under his sub interface.

{{{
mvpn.rams.ca.asr1#s ip b a su | i 172.16.30.2                                                  <- MVPN-FG P2P Interface under Customer VRF
172.16.30.2     4        64596   40047   38453 551850846    0    0 3w3d            1



mvpn.rams.ca.asr1#s ip b vpnv4 v masria-mgd nei 172.16.30.2 received-r   
Load for five secs: 2%/0%; one minute: 3%; five minutes: 3%
Time source is NTP, 15:30:51.102 CLT Thu Sep 17 2015

BGP table version is 551861212, local router ID is 217.139.253.22
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, x best-external, f RT-Filter, a additional-path
Origin codes: i - IGP, e - EGP, ? - incomplete

   Network          Next Hop            Metric LocPrf Weight Path
Route Distinguisher: 217.139.253.22:718 (default for vrf masria-mgd)
*  0.0.0.0          172.16.30.2                            0 64596 i                              <-- Default Route Advertised

Total number of prefixes 1 
}}}

== Check FortiGate-Ramsis BGP Session ==

- Check the BGP Session initiated to Pe-ramsis, where customers Public Subnets are advertized

{{{
pe.rams.ca.asr1#s ip b a su | i 172.29.10.78
172.29.10.78    4        64595   40028   38445  2289301    0    0 3w3d            2                      <- Shared Internet Pipe BGP Session


pe.rams.ca.asr1#s ip b nei 172.29.10.78 received-r 
Load for five secs: 13%/5%; one minute: 7%; five minutes: 7%
Time source is NTP, 15:37:11.081 CLT Thu Sep 17 2015

BGP table version is 2289303, local router ID is 217.139.253.151
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  41.187.9.93/32   172.29.10.78                           0 64595 i                                 <- Customers Public Subnets
 *>  41.187.9.104/29  172.29.10.78                           0 64595 i

Total number of prefixes 2 
}}}
