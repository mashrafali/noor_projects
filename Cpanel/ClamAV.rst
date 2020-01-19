{{{
Author       : Karim Farrag
Version      : 1.0
Creation date: 2014-12-3
Latest update: 2014-12-3
}}}

<<TableOfContents(3)>>

<<BR>>
== How to install ClamAV Scanner ==
<<BR>>
To install or uninstall ClamAV Scanner, use the Manage Plugins interface in WHM (Home >> cPanel >> Manage Plugins).<<BR>>

== ClamAV Scanner configuration ==
<<BR>>
To configure ClamAV Scanner: <<BR>>
<<BR>>
Select the services that you wish to scan. <<BR>>
- Scan Entire Home Directory — Scans the home directory on your server. <<BR>>
- Scan Mail — Scans all mail folders on your server. <<BR>>
- Scan Public FTP Space — Scans all folders that are publicly accessible through FTP services. <<BR>>
- Scan Public Web Space — Scans all folders that are publicly accessible through the web. <<BR>>
<<BR>>
Click Save. <<BR>>

== Configure ClamAV Scanner for specific users ==

If you wish to override the ClamAV Scanner configuration for specific users, click User Configuration. The User Configuration interface also allows you to set override defaults for all configured users. <<BR>>
Add or remove configured users <<BR>>

Before you can configure a user's ClamAV Scanner settings, that user must appear on the Configured Users menu. <<BR>>

To add a user to the Configured Users menu: <<BR>>
<<BR>>
Select the desired user from the User List menu. <<BR>>
<<BR>>
Click Add. <<BR>>

If you wish to add all of the users that are on the User List menu to the Configured Users menu, click Add All. <<BR>>

To remove a user from the Configured Users menu: <<BR>>

Select the desired user from the Configured Users menu. <<BR>>
Click Remove. <<BR>>
<<BR>>
If you wish to remove all of the users that are on the Configured Users menu, click Remove All. <<BR>>

Configure defaults for new configured users <<BR>>

ClamAV Scanner applies the settings that you specify under the Defaults header to all new configured users. <<BR>>

== To set the default settings for new configured users ==

    Select the services that you wish to scan. <<BR>>
        Scan Entire Home Directory — Scans the user's home directory. <<BR>>
        Scan Mail — Scans the user's mail folders. <<BR>>
        Scan Public FTP Space — Scans all of the user's folders that are publicly accessible through FTP services. <<BR>>
        Scan Public Web Space — Scans all of the user's folders that are publicly accessible through the web. <<BR>>
    Click Save.

Configure settings for an individual user <<BR>>

== To configure ClamAV Scanner for an individual user ==

    In the Group Scanner Configuration section's Configured Users menu, select the user for whom you wish to configure ClamAV Scanner.<<BR>>
        If the desired user does not appear in the Configured Users menu, use the instructions above to add that user. <<BR>>
        The user that you select will appear in the Configure User text box in the User Scanner Configuration section. <<BR>>
    In the User Scanner Configuration section, click Configure. <<BR>>
    Select the services that you wish to scan. <<BR>>
            Scan Entire Home Directory — Scans the user's home directory. <<BR>>
            Scan Mail — Scans the user's mail folders. <<BR>>
            Scan Public FTP Space — Scans all of the user's folders that are publicly accessible through FTP services. <<BR>>
            Scan Public Web Space — Scans all of the user's folders that are publicly accessible through the web. <<BR>>
    Click Save Defaults. <<BR>>
