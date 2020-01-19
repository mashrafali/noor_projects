## page was renamed from Enterprise/IT/Docs/SIPTrunk
{{{
Author       : Ashraf Ali
Version      : 1.0
Creation date: 2014-12-03
Latest update: 2014-12-04
}}}
<<TableOfContents(3)>>

= SIP trunk configuration =
The following are the steps to create a SIP trunk scenario.

• We already have Dokki and Ramsis SIP servers with 2 PRI.

• I will create two VM with all extensions, IVRs, queues, and ring groups, but without PRI cards.

• We need when a VM extension call an external number, the call will be forwarded to either Ramsis or Dokki PRI and dialed successfully.

• We need when a customer calls the Hotline 16700 or any of NOOR PRI numbers, the call will be received by one of the VM extension.

1- I created two VMs with IP addresses 192.168.0.10 and 192.168.0.20 and imported all the extensions, IVRs, queues, and ring groups.

2- We need to create two SIP trunks on both VMs; one to Ramsis SIP server and the other to Dokki SIP server:

SIP trunk to Ramsis configuration on the VM with IP address 192.168.0.10 (similar configuration is needed on the other VM with IP address 192.168.0.20)

{{attachment:S1.jpg}}

SIP trunk to Dokki configuration on the VM with IP address 192.168.0.10 (similar configuration is needed on the other VM with IP address 192.168.0.20)

{{attachment:S2.jpg}}

3- We need to create an outbound on the VM with the Dial pattern of external calls (8. means all numbers that start with 8) and the order of trunks to route from.

{{attachment:S3.jpg}}

4- We need create two SIP trunks on Dokki SIP server (one for each VM) (The same will be applied on Ramsis SIP server)

SIP trunk on Dokki SIP server to VM with IP address 192.168.0.10 (similar configuration is needed on Ramsis SIP server)

{{attachment:S4.jpg}}

SIP trunk on Dokki SIP server to VM with IP address 192.168.0.20 (similar configuration is needed on Ramsis SIP server)

{{attachment:S5.jpg}}

5- Finally we need to choose which VM we will forward incoming calls coming to Hotline 16700 or NOOR PRI numbers (In the following case I choose VM with IP address 192.168.0.10)

{{attachment:S6.jpg}}
