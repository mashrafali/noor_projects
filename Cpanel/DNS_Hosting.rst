{{{
Author       : Ayman Tohamy
Title        : cPanel Shared DNS Hosting
Version      : 1.1
Creation date: 2016-02-15
Latest update: 2016-02-15
}}}
<<TableOfContents(4)>>

== Overview ==

Actually we need to configure our cPanel for shared hosting customers whom needs to fully mange their DNS zones including all types of DNS records (A - MX - Cname - TXT -SRV -,....etc.).

== Configure DNS only shared hosting service ==
=== Create new DNS Only Feature ===

 1. Login to cPanel WHM.
 1. Once you are in the WHM, look for the '''Feature Manager''' tool which is located under the '''Packages''' category.<<BR>>

 .{{attachment:1.jpg}}

 1. This leads you to a page where you can select the feature template  list you want to edit. In this case, there is not one, so we can create  one by giving a name to the list we want to create. For example, we name  this list '''DNS Only''' Click '''Add''' to create the feature template.

 .{{attachment:3.jpg}}

 1. The new list name will appear under hte '''Edit a Feature List - DNS Only''' section. Make sure it is selected in the dropdown and click the '''Edit'''  button. From here you will see a long lost of Features that are offered  for accounts with this list. Select the checkbox next to  the '''Advanced DNS Zone Editor''' field and below fields.<<BR>>

 .{{{
Edit new created feature list "DNS Only" to include below items:
- Ability to Change MX
- Addon Domain Manager
- Advanced DNS Zone Editor
- Change Language
- Change Style
- Network Tools
- Password Change
- Subdomain Manager
- Theme Switching
- Update Contact Information
- Video Tutorials
}}}

 .{{attachment:2.jpg}}


 1. Scroll to the bottom and click on the Save button. This saves your  configuration of new feature list '''DNS Only'''.

=== Create new DNS Package ===

Create new DNS package as per below and please ensure that choose ''' DNS Only''' for Feature List 

 .{{attachment:4.jpg}}


 .{{attachment:5.jpg}}

=== Create cPanel Account ===

Create cPanel account and linked it with ''' DNS ''' package.

Now you will be able add / Modify your own DNS zone.

 .{{attachment:6.jpg}}
