{{{
Author       : Mohamed Ashraf
Title        : FortiGate UTM Customer Provionsing
Version      : 1.0
Creation date: 25/8/2015
Latest update: 27/8/2015
}}}

<<TableOfContents(4)>>


{{attachment:logo1.jpg}}


= Introduction =

'''* Before We Begin Provisioning Customers we need to understand the following:'''

[[Enterprise/IT/Docs/HowTo/Linux/FortiGate-DC/L3-Design           | 1- The Layer-3 Design of the FortiGate UTM]]

[[Enterprise/IT/Docs/HowTo/Linux/FortiGate-DC/FA-L3-Design        | 2- The Layer-3 Design of the FortiAnalyzer]]

'''* After That the Provisioning Points can be summarized as below:'''

1- Create Customer VDOM.

2- Create Customer Interfaces.

3- Create User For Customer Access.

4- Config Customer VDOM Logging Space.

5- Config Customer Traffic Routing.

6- Config Customer Log Settings.

7- Create Customer VDOM NOOR-DEFAULT Security Profiles

8- Create Customer Public IP-Pool.

9- Create Customer Firewall Policy.

10- Create ROOT Firewall Policy For customer.

11- Config ROOT Routing For Customer Analyzer Access.

12- Advertise Customer Public Subnet.


= Configuration =

- This Guide will use the Fortigate WEB Gui & the CLI interface in some configuration parameters, prepare both interfaces before configuration.

- The UTM CLI Management can be accessed via SSH


== Create Customer VDOM ==

- In the Global Config Click on "VDOM".

{{attachment:FG-1.png}}

- Click on Create New, to create a New VDOM for the Provisioned Customer.

{{attachment:FG-2.png}}

- In the Image Below:

1- Name the New VDOM, Preferred to use customer name

2- Choose NAT Mode, so that the customer Tenant is routing capable.

3- Add any preferred comment for customer VDOM.

{{attachment:FG-3.png}}

== Create Customer Interfaces ==

- In the Image Below:

1- In Global, Click on Network > Interfaces

2- Click on Create New.

{{attachment:FG-4.png}}

- In the Image Below:

1- Name the Customer Interface, Use naming of [Customer Vlan-Customer name]  ex: 3002-Mansour

2- Select Type Vlan

3- Select port2 for Inbound Customer Traffic.

4- Enter Assigned Customer Vlan

5- Attach the Interface to the Customer VDOM that you have created.

6- Enter the Point to Point /30 Ip address of the customer VRF. (Provided by Core Team)

7- Enable HTTP-HTTPS-PING-FCT ACCESS on the interface so that the customer can reach & manage his UTM.

8- Select Detect & identify for customer Iface.

9- Add the description that you like.

10- Click on Apply

{{attachment:FG-5.png}}

- Create a VDOM-Link Interface.

{{attachment:FG-6.png}}

- In the Image Below:

1- Enter Vdom Link Name, follow the created vdom links naming styles.

2- Choose ROOT as the Starting vdom end.

3- Enter a /30 IP subnet range for the vdom link, so that the two vdoms are reachable via this /30 link.

4- Allow ping only on root end

5- Add any descriptions needed.

6- Choose customer VDOM as the other end.

7- Enter the Other point of the /30 subnet entered above.

8- Allow ping from customer side.

9- Add any descriptions needed. (this will viewed by customer)

10- Click OK to create the interface.

{{attachment:FG-7.png}}

== Create User For Customer Access ==

- In the Image Below:

1- In global click on Admin > Administrators

2- Click on Create new, to create a username/pass & privilege level for customer.

{{attachment:FG-8.png}}

- In the Image Below:

1- Give the customer a Username

2- Enter initial customer password.

3- Confirm Password.

4- Add Needed Descriptions.

5- Give the Customer the Privilege Profile of "CUSTOMER".

6- Choose the Created VDOM of the Customer, so that the username created can access it's VDOM.

7- Click on OK to confirm creation.

{{attachment:FG-9.png}}

== Config Customer VDOM Logging Space ==

- In the Image Below:

1- Under Virtual Domains, click on Customer VDOM > System > Config > Advanced.

2- Click on the Edit Icon.

{{attachment:FG-10.png}}

- Set All Logging Values to 100Migs to limit the customer Logs on the Local Disk on the UTM.

{{attachment:FG-11.png}}

== Config Customer Traffic Routing ==

- Customer has static Route Configuration & BGP routing Configuration with MVPN

=== Static Routing ===

- In the Image Below:

1- Under Virtual Domains, click on Customer VDOM > Router > Static > Static routes

2- Click on Create new.

{{attachment:FG-12.png}}

- In the Image Below:

1- Enter the Default route ip/mask values (0.0.0.0/0.0.0.0)

2- Enter Created Customer VDOM-LINK Interface.

3- Enter Other End GW IP of the VDOM Link

4- ADD Description as needed.

5- click on OK to create static route.

{{attachment:FG-13.png}}

=== BGP Routing ===

- In the Image Below:

1- Under Virtual Domains, click on Customer VDOM > Router > Dynamic > BGP

2- Add the Local BGP Router AS Number (Core Provided)

3- Configure the router ID, SAME as the IP of the P2P interface of the CUSTOMER Vlan Interface.

4- Configure Neighbor IP as the other end of the /30 subnet entered above.

5- Enter NOOR Global AS Number.

6- Click on ADD to create the BGP Session.

{{attachment:FG-14.png}}

- To allow the Customer BGP Session to Advertise its default route under the customer vrf, the following step is needed:

- IN THE CLI TERMINAL; Enter the following Commands.

{{attachment:FG-22.txt}}

== Config Customer Log Settings ==

- In the Image Below:

1- Under Virtual Domains, click on Customer VDOM > Log & Report > Log Config > Log Settings

2- Enable Disk Logging & Disable Local Reports, as the Analyzer is Responsible for Reporting.

3- Make Sure Every Event Type are Enabled for Logging.

4- Make sure all local Traffic are logged.

5- Choose Disk For GUI Log View.

6- Enable Name Resolving in Logs.

7- Click on ok To Apply.

{{attachment:FG-15.png}}

== Create Customer VDOM NOOR-DEFAULT Security Profiles ==

- IN the CLI Interface:

1- Enter the Following commands:

{{{
config vdom
edit <CUSTOMER VDOM NAME>
}}}

2- Then copy and paste the following into the CLI to Create ALL NOOR-DEFAULT Security Profiles for the Provisioned Customer.

{{attachment:FG-16.txt}}

== Create Customer Public IP-Pool ==

- In the Image Below:

1- Under Virtual Domains, click on Customer VDOM > Policy & Objects > Objects > Ip Pools.

2- Click on Create new

{{attachment:FG-17.png}}

- In the Image Below:

1- Name The customer IP-POOL.

2- Add Description as needed.

3- Choose Overload Type for initial Configuration.

4- Enter the Customer PUBLIC IPs RANGE (provided by Core)

5- Click on OK to confirm.

{{attachment:FG-18.png}}

== Create Customer Firewall Policy ==

- In the Image Below:

1- Under Virtual Domains, click on Customer VDOM > Policy & Objects > Policy > IPV4

2- Click on Create New.

{{attachment:FG-19.png}}

- In the Image Below:

1- Choose Any incoming Interface inside customer VDOM.

2- Source Address can be set to all.

3- Set the customer VDOM LINK as the outgoing interface.

4- Destination address set to all.

5- Schedule set to always.

6- Service set to all.

7- Action is Accept to allow this traffic to pass.

8- Activate natting for the customer outbound traffic.

9- Use Dynamic IP Pool and choose the Pool that was pre-created for the Customer (PUBLIC IP POOL)

10- Use Pre-Created NOOR-DEFAULT Profile for AntiVirus.

11- Use Pre-Created NOOR-DEFAULT Profile for Web Filter.

12- Use Pre-Created NOOR-DEFAULT Profile for Application Control.

13- Use Pre-Created NOOR-DEFAULT Profile for IPS.

14- Use Pre-Created NOOR-DEFAULT Profile for Email Filter.

15- Use Pre-Created NOOR-DEFAULT Profile for DLP Sensor.

16- Use Pre-Created NOOR-DEFAULT Profile for VOIP.

17- Use Pre-Created NOOR-DEFAULT Profile for ICAP.

18- Enable Log Allowed Traffic For ALL Sessions & Enable Log Generation on session start.

19- Enable the Created Policy.

20- Click on OK To confirm Default Policy Creation for the Customer.

{{attachment:FG-20.png}}

{{attachment:FG-21.png}}

== Create ROOT Firewall Policy For customer ==

- In the Image Below:

1- Under Virtual Domains, click on ROOT VDOM 

2- Click on Policy & Objects > Policy > IPV4

3- Click on Create new.

{{attachment:FG-23.png}}

- In the Image Below:

1- Choose Incoming Interface of the Customer VDOM LINK Linterface.

2- Source address can be all.

3- Source Device Type set to all.

4- Select the outgoing interface of PORT3 (The Shared Internet Interface)

5- Destination Address can be set to all.

6- schedule set to Always.

7- Service set to all.

8- Action is set to accept to allow above defined traffic to pass through our ROOT Tenant.

9- No Natting Is needed as the customer is already Natted from his VDOM.

10- No Security Features are needed as the customer already has those activated in his VDOM.

11- Police the customer Traffic Based on his subscribed internet Speed, you can select the suitable Police-Profile.

12- Enable Log Allowed Traffic For ALL Sessions & Enable Log Generation on session start.

13- Enable the Created Policy.

14- Click on OK To confirm Default Policy Creation for the Customer.


{{attachment:FG-24.png}}

{{attachment:FG-25.png}}


== Config ROOT Routing For Customer Analyzer Access ==

- In the Image Below:

1- Under Virtual Domains, click on ROOT VDOM 

2- Click on Router > Static > Static Routes

3- Click on Create new.

{{attachment:FG-26.png}}

- In the Image Below:

1- Enter The customer Public Subnet (The customer Arrives at root VDOM Already Natted)

2- Enter the Customer VDOM LINK Interface.

3- Enter Gateway of Customer End.

4- Enter Description as needed.

5- Click on OK to Confirm Creation.

{{attachment:FG-27.png}}

== Advertise Customer Public Subnet Via BGP ==

* For the Customer Traffic To return Through the FG UTM, We need to inform the CORE MPLS Cloud about the customer subnet that is protected by the UTM.

- In the Image Below:

1- Under Virtual Domains, click on ROOT VDOM 

2- Click on Router > Dynamic > BGP

3- Enter the Customer PUBLIC Subnet IP/Netmask

4- Click on ADD to start advertising the subnet to PE-Ramsis

{{attachment:FG-28.png}}

= The Final Steps =

- To Finalize Customer provisioning the following Steps are needed.

== Provision the Customer Analyzer ==

- You can Provision the Customer Analyzer Access by using [[Enterprise/IT/Docs/HowTo/Linux/FortiGate-DC/FA-Customer-initial | THIS]] Guide.

== Provide the customer FG Interface ==

1- The customer will Access his FG through the point to point VLAN interface IP assigned to his Tenant, which was pre-configured above.

2- Provide Customer Username/Password, that were pre-configured as per steps above

== Provide the customer Analyzer Interface ==

1- The customer will Access his Analyzer through the global united IP of the Forti-Analyzer ('''172.100.100.100''')

2- Provide Customer Username/Password, that were pre-configured in [[Enterprise/IT/Docs/HowTo/Linux/FortiGate-DC/FA-Customer-initial | THIS]] Guide.


= Fortigate Static NAT Configuration =

{{attachment:FGSNAT.png}}

- Before we can access the NAT IP Address, we have to create a Virtual IP using the following steps:


1. Go to '''Firewall Objects > Virtual IP > Virtual IP'''.

2. Select '''Create New'''.

3. Complete the following and select '''OK'''.

 * Name : '''Web_Server_NAT (can be filled with any names)'''

 * External Interface  : '''wan1'''

 * Type  : '''Static NAT'''

 * External IP Address/Range: '''200.200.100.100'''

 * Mapped IP Address/Range: '''10.0.10.100'''

 * No Port Forwarding Selected


- After finishing create the Virtual IP then Create the Policy using the following steps:


1. Go to '''Policy> Policy > Policy'''  and select  '''Create New'''

2. Complete the following and select '''OK'''.

3. Here is the form:

 * Source Interface/Zone: '''wan1'''

 * Source Address: '''All'''

 * Destination Interface/Zone: '''Internal'''

 * Destination Address: '''Web_Server_NAT (select from the one we have created on above steps)'''

 * Schedule : '''always'''

 * Service : '''ANY'''

 * Action: '''ACCEPT'''

 * Select the '''NAT''' option

 * Select '''OK'''


- After completing all the steps above then test using ping to 200.200.100.100 from Internet and it should be success.
