{{{
Author       : Mohamed Ashraf
Title        : FortiAnalyzer Customer Provisioning
Version      : 1.0
Creation date: 26/8/2015
Latest update: 26/8/2015
}}}

<<TableOfContents(4)>>


{{attachment:logo1.jpg}}


= Step-1: Connect to Anayzer =

- Connect to the FortiAnalyzer Box Via its Web GUI.

- Login Using the Admin Account

{{attachment:FA-1.jpg}}


= Step-2: Create Customer ADOM =

- To create Customer ADOM, Click on "System Settings"

{{attachment:FA-2.jpg}}

- Then Click on "All ADOMs"

{{attachment:FA-3.jpg}}

- Click on "Create new"

{{attachment:FA-4.jpg}}

- In the Settings Below:

1- Choose FortiGate VDOM of Customer.

2- Click on "ADD" to Link Customer VDOM with his Analyzer ADOM.

3- Click on "OK"

{{attachment:FA-5.jpg}}

= Step-3: Create Customer User =

- Now that we have created the Customer ADOM, now we have to create his User account and link it to his ADOM.

- In the Settings Below:

1- Click on "Administrator"

2- Click on "Create new"

{{attachment:FA-6.jpg}}

- In the Settings Below:

1- Type Customer Username (Preferred Customer Name or Name that matches his Fortigate Vdom)

2- Type Customer Description.

3- Type Password for customer.

4- Confirm Account Password.

5- Choose "Customer" Admin Profile in the drop down list.

6- Choose "Specify" Option for Administrative Domain.

7- Select the Pre-created ADOM.

8- Click on "OK"

{{attachment:FA-7.jpg}}


* After Applying Above Configuration, The customer can now reach the Analyzer from his site @ United Analyzer IP of ('''172.100.100.100''')
 
