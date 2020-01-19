{{{
Author       : Mohamed Ashraf
Title        : FortiGate UTM Vdom Design Drafts
Version      : 1.0
Creation date: 26/3/2015
Latest update: 26/3/2015
}}}


<<TableOfContents(4)>>



{{attachment:logo1.jpg}}


= Device Used For POC =

- '''''Fortigate 3140B'''''

{{attachment:the-box.jpg}}

= Designs For Operation Scenarios =

- The following Vdom Designs where constructed to satisfy & cover all use cases for our DC/managed internet Customers.

== Scenario 1 : Managed Internet Customer Only ==

{{attachment:forti-1.jpg}}

== Scenario 2 : Dedicated Server Hosting Only ==

=== Private Hosting, Customer Managed ===

{{attachment:forti-2.jpg}}

=== Private Hosting, NOOR Managed ===

{{attachment:forti-3.jpg}}

=== Public Hosting, Customer Managed ===

==== Customer is in NOOR Network ====

{{attachment:forti-4.jpg}}

==== Customer is outside NOOR Network ====

{{attachment:forti-5.jpg}}

=== Public Hosting, NOOR Managed ===

==== Customer is in NOOR Network ====

{{attachment:forti-6.jpg}}

==== Customer is outside NOOR Network ====

{{attachment:forti-7.jpg}}

== Scenario 3 : Shared Hosting Only ==

{{attachment:forti-8.jpg}}

= NOOR VDOM =

== Vdom Interconnection Design ==

{{attachment:forti-9.jpg}}

== Vdom Full HA Design ==

{{attachment:forti-10.jpg}}
