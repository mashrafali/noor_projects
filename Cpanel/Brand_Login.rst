{{{
Author       : Ayman Tohamy
Title        : Customize cPanel Login (NOOR Brand)
Version      : 1.1
Creation date: 2016-02-04
Latest update: 2016-02-04
}}}
<<TableOfContents(4)>>

== Overview ==
This document describes the steps to create server-wide custom login pages for the cPanel, WHM, and Webmail interfaces.

Notes:

 * Throughout this document, themename is the name of your custom theme.

 * For more information about cPanel & WHM's login theme, read our [[https://documentation.cpanel.net/pages/viewpage.action?pageId=11501661|The cPanel & WHM Default Login Theme]] documentation.
 * You '''cannot''' modify login pages for individual cPanel accounts.
 * Resellers '''cannot''' create their own custom login pages.

== How to create custom-branded login pages ==
----
{{attachment:no.1.jpg}}

=== Duplicate the existing login theme. ===
To duplicate the existing login theme, perform the following steps:

 1. Navigate to WHM's [[https://documentation.cpanel.net/display/ALD/Theme+Manager|Theme Manager]] interface (''Main >> Themes >> Theme Manager''). Note:
 In cPanel & WHM version 11.48 and earlier, navigate to WHM's ''Universal Theme Manager'' interface (''Home >> Themes >> Universal Theme Manager'').

 1. Click ''Manage Themes'' under the ''Login'' icon.

 .{{attachment:1.jpg}}

 1. Click ''Clone'' next to the theme that you wish to duplicate.

 .{{attachment:2.jpg}}

 1. Enter a name for the duplicate theme in the available text box and click ''Submit''.

 .{{attachment:3.jpg}}

 .{{attachment:4.jpg}}


WHM saves your new theme in the /usr/local/cpanel/base/unprotected/themename/ directory, where themename is the name that you entered.

 .{{attachment:5.jpg}}

 .{{attachment:6.jpg}}

----
{{attachment:no.2.jpg}}

=== Modify the login page subheader logos. ===
Replace any or all of the subheader images in the /usr/local/cpanel/base/unprotected/themename/images/ directory.

 * Subheader images appear at the top of the login pages for cPanel, WHM, and Webmail.
 * Make '''certain'''  that your replacement images use the correct dimensions in order to  allow for the appropriate amount of padding within the login subheader  containers.
 Click to view subheader logo images in cPanel & WHM's default login theme...
 ||<tableclass="confluenceTable tablesorter tablesorter-default stickyTableHeaders"class="confluenceTd">                 images/cpanel-logo.png               ||<class="confluenceTd">The cPanel subheader.||<class="confluenceTd">50||<class="confluenceTd">233||<class="confluenceTd"> {{https://documentation.cpanel.net/download/attachments/11501702/cpanel-logo.png?version=3&modificationDate=1441132028620&api=v2||data-linked-resource-version="3",data-linked-resource-content-type="image/png",data-image-src="/download/attachments/11501702/cpanel-logo.png?version=3&modificationDate=1441132028620&api=v2",data-linked-resource-default-alias="cpanel-logo.png",data-linked-resource-container-id="11501702",data-linked-resource-type="attachment",data-linked-resource-container-version="3",data-linked-resource-id="11501719",data-base-url="https://documentation.cpanel.net",class="confluence-embedded-image image-center",data-unresolved-comment-count="0"}} ||
 ||<class="confluenceTd">                 images/whm.png               ||<class="confluenceTd">The WHM subheader.||<class="confluenceTd">55||<class="confluenceTd">208||<class="confluenceTd"> {{https://documentation.cpanel.net/download/attachments/11501702/whm.png?version=3&modificationDate=1441132030729&api=v2||data-linked-resource-version="3",data-linked-resource-content-type="image/png",data-image-src="/download/attachments/11501702/whm.png?version=3&modificationDate=1441132030729&api=v2",data-linked-resource-default-alias="whm.png",data-linked-resource-container-id="11501702",data-linked-resource-type="attachment",data-linked-resource-container-version="3",data-linked-resource-id="11501741",data-base-url="https://documentation.cpanel.net",class="confluence-embedded-image image-center",data-unresolved-comment-count="0"}} ||
 ||<class="confluenceTd">                 images/whm-white.png               ||<class="confluenceTd">The WHM subheader for non-white backgrounds.||<class="confluenceTd">55||<class="confluenceTd">208||<class="confluenceTd"> {{https://documentation.cpanel.net/download/attachments/11501702/whm-white.png?version=3&modificationDate=1441132030669&api=v2||data-linked-resource-version="3",data-linked-resource-content-type="image/png",data-image-src="/download/attachments/11501702/whm-white.png?version=3&modificationDate=1441132030669&api=v2",data-linked-resource-default-alias="whm-white.png",data-linked-resource-container-id="11501702",data-linked-resource-type="attachment",data-linked-resource-container-version="3",data-linked-resource-id="11501740",data-base-url="https://documentation.cpanel.net",class="confluence-embedded-image confluence-content-image-border image-center",data-unresolved-comment-count="0"}} ||
 ||<class="confluenceTd">                 images/webmail.png               ||<class="confluenceTd">The Webmail subheader.||<class="confluenceTd">50||<class="confluenceTd">306||<class="confluenceTd"> {{https://documentation.cpanel.net/download/attachments/11501702/webmail.png?version=3&modificationDate=1441132030558&api=v2||data-linked-resource-version="3",data-linked-resource-content-type="image/png",data-image-src="/download/attachments/11501702/webmail.png?version=3&modificationDate=1441132030558&api=v2",data-linked-resource-default-alias="webmail.png",data-linked-resource-container-id="11501702",data-linked-resource-type="attachment",data-linked-resource-container-version="3",data-linked-resource-id="11501739",data-base-url="https://documentation.cpanel.net",class="confluence-embedded-image",data-unresolved-comment-count="0"}} ||



 .{{attachment:7.jpg}}
----
{{attachment:no.3.jpg}}

=== Modify other login page images. ===
Replace any or all of the other login theme images in the /usr/local/cpanel/base/unprotected/themename/images/ directory.

 . below list of NOOR Brand Login pages

cPanel Login :- 
 .{{attachment:cpanel-logo.png}}

Webmail Login:- 
 .{{attachment:webmail.png}}

WHM Login:- 
 .{{attachment:whm.png}}

Replace these Images with current cPanel-NOOR images :-

 .{{attachment:8.jpg}}

----
{{attachment:no.4.jpg}}

=== Modify the login page templates. ===
Make  the desired changes to your custom theme's other login page templates.  Login themes include the main templates that all users view when they  log in, error page templates, and templates for the ''Reset Password'' feature.

 * These template files control what users see when they log in, encounter login errors, or reset their passwords.
 * Advanced users can modify the main.tmpl and resetpass.tmpl files to change how the system processes user input.





----
{{attachment:no.5.jpg}}

=== Modify theme CSS files. ===
Make the desired changes to the style_v2.css and style_v2_optimized.css files.

Note:

 . Subheader logo customizations may require changes to the subheader logo style attributes. Modify these attributes under the login-sub-header div tag in the style_v2.css file.

----
{{attachment:no.6.jpg}}

=== Update your server's locales. ===
If you added new text to your theme's login templates, add those new phrases to your server's locales.

 * To do this, use WHM's '' [[https://documentation.cpanel.net/display/ALD/Edit+a+Locale|Edit a Locale]] '' interface (''Home >> Locale >> Edit a Locale'').
 * For more information about cPanel & WHM's localization system, read our [[https://documentation.cpanel.net/display/SDK/Guide+to+Locales|Guide to Locales]] documentation.

----
{{attachment:no.7.jpg}}

=== Use your new custom login theme. ===
To cause all of your server's accounts to use the new custom login theme, select the theme's name from the ''Default login theme'' menu in WHM's [[https://documentation.cpanel.net/display/ALD/Tweak+Settings|Tweak Settings]] interface (''Home >> Server Configuration >> Tweak Settings'').

 .{{attachment:9.jpg}}


Test New cPanel Login :  https://cpanel.noorhosting.com:2083/ 

 ..{{attachment:11.jpg}}
