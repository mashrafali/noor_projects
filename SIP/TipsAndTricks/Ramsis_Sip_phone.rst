Describe Enterprise/IT/Docs/HowTo/Linux/SIP/TipsAndTricks/Ramsis_Sip_phone here.


{{{
Author       : Mahmoud Hamdy
Version      : 1.0
Creation date: 2015-02-17
}}}

<<TableOfContents(3)>>

== How to Troubleshoot in Ramsis IP Phone ==

=== Open Pe-auto and Check on the VRF configuration ===

{{{

pe.auto.alx.asr1#sh running-config vrf pop-voip
Load for five secs: 4%/1%; one minute: 3%; five minutes: 3%
Time source is NTP, 17:03:45.574 CLT Tue Feb 17 2015

Building configuration...

Current configuration : 1037 bytes
ip vrf pop-voip
 rd 217.139.253.152:131
 import map corptovoip
 route-target export 20928:131
 route-target export 20928:10
 route-target import 20928:131
 route-target import 20928:10
!
!
interface GigabitEthernet0/0/2
 description "Connected to agg.auto.alx.3560 G0/25"
 mtu 1516
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.1000
 description Connected to Auto IP Phone
 encapsulation dot1Q 1000
 ip vrf forwarding pop-voip
 ip address 10.20.11.9 255.255.255.252
!
interface ATM0/1/0
 description Connected to Auto-DSLAM
 no ip address
 load-interval 30
 atm clock INTERNAL
 atm mcpt-timers 30 60 90 
 cell-packing 28 mcpt-timer 3
!         
interface ATM0/1/0.1403 point-to-point
 description IP Phone @ Aauto CO.
 ip vrf forwarding pop-voip
 ip address 10.20.11.9 255.255.255.252
 shutdown 
 pvc 15/203 
  class-vc L3-512K
 !        
!         
router bgp 20928
 !        
 address-family ipv4 vrf pop-voip
  redistribute connected
  redistribute static
 exit-address-family
!         
ip route vrf pop-voip 192.168.11.8 255.255.255.252 ATM0/1/0.1403
end 

}}}

=== ping 192.168.10.10 to make sure that the pop-vrf can reach to to our sip server ===

{{{

pe.auto.alx.asr1#ping vrf pop-voip ip 192.168.10.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/9/14 ms
}}}


=== ping 10.20.11.10 to make sure that there is no physical problem ===

{{{

pe.auto.alx.asr1#ping vrf pop-voip ip 10.20.11.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.20.11.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/9/14 ms
}}}



=== Finally check on the phone configuration and make sure that the L2QOS is vlan 1000 ===
