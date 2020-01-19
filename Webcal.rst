## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Webcal
{{{
Author       : Ayman Tohamy
Version      : 1.1
Creation date: 2012-11-15
Latest update: 2012-11-15
}}}
<<TableOfContents(4)>>

= WebCalendar System Administrator's Guide =
'''WebCalendar Version: 1.2.b1'''

== Table of Contents ==
----
== Introduction ==
WebCalender is an open source PHP-based multi-user calendar.

'''Features:'''

 * Multi-user support
 * Group support
 * View day-at-glance
 * View month-at-glance
 * View week-at-glance
 * View year-at-glance
 * View another user's calendar
 * View multiple users' calendars at the same time
 * View one or more users' calendar via layers on top of your own calendar
 * Public calendar (that requires no login) where anonymous users     submit events that are approved by an administrator
 * Add/Edit/Delete users
 * Add/Edit/Delete events/todos
 * Repeating events
 * Custom event fields
 * Search interface for calendar entries
 * User-configurable preferences for colors, 12/24 time format,     Week start on Sun or Mon, default work hours
 * Online help
 * Checks for scheduling conflicts
 * Support for multiple timezones
 * Users can accept or reject events added by another user to their calendar
 * Email reminders
 * Email notifications for new events
 * Support, at least partially, for more than 40 different languages:
  * Afrikaans
  * Albanian
  * Arabic
  * Basque
  * Bulgarian
  * Catalan
  * 繁體中文(Big5)
  * 简体中文(GB2312)
  * Croatian
  * Czech
  * Danish
  * Deutsche (German)
  * Elven (JRR Tolkien's "''Lord of the Rings''") Requires a special font.
  * English
  * Español (Spanish)
  * Estonian
  * Français (French)
  * Galician
  * Greek
  * Hebrew
  * Hollands (Dutch)
  * Holo (Taiwanese)
  * Hungarian
  * Icelandic
  * Italiano (Italian)
  * Japanese (EUCJP, SJIS)
  * Korean
  * Lithuanian
  * Norsk (Norwegian)
  * Polish
  * Portuguese
  * Portuguese/Brazil
  * Romanian
  * Русско (Russian)
  * Serbian
  * Slovak
  * Slovenian
  * Suomalainen (Finnish)
  * Svensk (Swedish)
  * Turkish
  * Welsh
 * Exporting to and importing from:
  * Palm Pilot
  * iCalendar
  * vCalendar
  * hCalendar (import only, requires PHP5)
 * Authentication using:
  * web-based
  * HTTP authentication
  * LDAP
  * NIS
  * IMAP
  * support for external application authentication ( Postnuke, Joomla )
 * Activity log that tracks:
  * event creation
  * event updates
  * event acceptance
  * event rejection
  * email notifications
  * email reminders
 * Synchronization and Syndication:
  * Two-way authenticated iCalendar (ics) sync
  * Anonymous iCalendar (ics) publishing
  * RSS 2.0 feed

[[#|↑ top]]

----
== System Requirements ==
 * PHP 4.1 - PHP 5: some features specific version:
  * two-way iCalendar sync: PHP 4.3+
  * hCalendar importing: PHP 5+
 * Database (see below]])
 * CSS-enabled browser:
  * Microsoft Internet Explorer
  * Mozilla Firefox
  * Opera
  * Apple Safari
 * JavaScript-enabled browser
 * If not using HTTP-based authentication,       then browser cookies are required

'''Recommended:'''

 * pilot-link (if exporting to Palm):   http://sourceforge.net/project/?group_id=2158 [[http://sourceforge.net/project/?group_id=2158|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]

:

 * MySQL
 * Oracle 8+
 * PostgreSQL
 * Interbase
 * MS SQL Server
 * SQLLite (PHP5)
 * IBM DB2
 * ODBC

{{{
TIP:
PHP ODBC includes support for Adabas D, Solid    and Sybase SQL Anywhere as well as ODBC. PHP5 comes bundled with    SQLite support. SQLite is an embedded file-based database. If your    hosting service supports PHP5, you should be able to create as    many SQLite databases as you like (since each database is just    a file).
}}}
For the database you choose, you must have its drivers built into   PHP. For example, to use MySQL, PHP must be compiled with MySQL   support (which is the default setting when installing PHP).   See the PHP pages ([[http://www.php.net|www.php.net]] [[http://www.php.net|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]])   for more information on setting up PHP.

No optional PHP packages (other than MySQL) are required for this   application. However, PHP should be compiled with {{{--enable-track-vars}}}   on some systems.

{{{
TIP:
If you want to use gradient background    images, PHP must be compiled with the GD library.
}}}
You can run PHP either as a CGI or an Apache module. You'll get better   performance with PHP setup as a module. Not only will you not have to   deal with the CGI performance hit, but you'll be able to use PHP's   database connection pooling. Additionally, this application can use   a form/cookie-based authentication or traditional HTTP authentication.   For traditional HTTP authentication, PHP must be built as an Apache   module.

If you are planning on using email reminders, you will need to build   PHP as a CGI in order to run the {{{send_reminders.php}}} script. I would   strongly recommend building a module-based PHP for your web server   and then a second PHP build to create the CGI version.

{{{
TIP:
 Some Linux distributions come with both a module-based PHP   with Apache and a standalone PHP binary.   Check for {{{/usr/bin/php}}} to see if you already have the   PHP standalone executable. If it's there, you can use   the following command to see what version of PHP you have:

 . {{{/usr/bin/php -v}}}

}}}

[[#|↑ top]]

----
== File Unpacking ==
Unpack the calendar software in its own directory somewhere where   your web server will find it. (See your web server documentation for information.)

By default, unpacking WebCalendar will create its own directory   when you unpack it. The new directory name will typically   contain the version name (such as {{{WebCalendar-1.3.0}}}).   You can rename this directory after unpacking the files if you   prefer a directory name like {{{calendar}}} or {{{webcalendar}}}.   Keep in mind that unless you remap the directory (via your web server's   configuration settings), it will be part of the URL for the calendar.

[[#|↑ top]]

----
== Application Installation ==
WebCalendar now includes an installation script that will guide you through  the entire installation process.  It should run automatically when first accessing  a new installation or when a major version upgrade has been performed.  The installation  script can always be rerun if needed by adding {{{install/index.php}}}  to your base WebCalendar URL.

=== WebCalendar Installation Wizard: Step 1 ===
When first run, the installation script will prompt you for a  ''Configuration Wizard Password'' that will protect against  unauthorized  tampering. Until this password is set, your WebCalendar installation  and possibly your database are vulnerable to attack. Once, you've set  your password, be sure  to write it down. If forgotten, the only way to recover is to manually  edit the  {{{install/settings.php}}} file.

The installation script will now check several prerequisites and report the  results. Any item that appears in RED should be   corrected before continuing.
||Prerequisite Failed ||Recommended Solution ||
||PHP 4.1.0 or greater ||Upgrade your PHP installation ||
||Safe Mode* = Off ||If possible, change this setting in your {{{php.ini}}} file to {{{Off}}}. ||
||Safe Mode Allowed Vars* ||If unable to to set Safe Mode Off, then set safe_mode_allowed_vars to contain    {{{TZ}}} in your {{{php.ini}}} file. ||
||||<style="text-align:center">* NOTE: If neither of the these settings pass testing, then    your WebCalendar installation will be restricted to using the server's timezone    setting only. ||
||File Uploads ||If disabled, then some features of WebCalendar will not function. This setting is configurable in your {{{php.ini}}} or {{{httpd.conf}}} files as {{{file_uploads}}} ||
||CRYPT_STD_DES ||This setting should be able to be ignored. We now support multiple {{{crypt}}}    algorithms. If this setting is not enabled and you experience difficulties logging in,     please notify the WebCalendar development team. ||
||GD ||This library is required only if gradient background images are  desired. Normally, PHP must be compiled with this option. Some  installations only require it be enabled in your    {{{php.ini}}} file. ||
||Session Check ||PHP Sessions are used during the installation process and are required      for proper operation. Sessions can be configured in your {{{php.ini}}} file.      If it's not possible to enable sessions, then you must manually install WebCalendar.      Details are available in    [[#Appendix_F:_Manual_Installation|Appendix F]] ||
||Settings.php Status ||The installation script was either unable to create, or unable to write      to your settings.php file. You will need to modify the permissions of the {{{includes}}}     directory. On Linux/UNIX, you should change this directory to be read/write      for all users ({{{chmod 777 includes}}}). On Windows, change this directory       to have full access for all users using Windows Explorer. ||




=== WebCalendar Installation Wizard: STEP 2 ===
The installation script can create your WebCalendar database as well  as create all the required tables and pre-populate them with the basic  configuration data. These features are only available when using fully  supported database types. See  [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#appendixB|Appendix B]] to manually create your database if needed.

'''Database Status'''

Displays the current status of your database settings as well the supported  databases for your PHP installation.

'''Database Settings'''

To configure your database access. Set the values for:
||<tableclass="distinguish">Displayed Value ||settings.php value ||Comments ||
||Database Type ||db_type ||One of "mysql", "oracle", "postgresql", "odbc",    "mssql", sqllite, db2,    or "ibase" ||
||Server ||db_host ||The hostname that database is running on.    (Use localhost if it's the same machine as    the web server.) (This variable is not used with ODBC, enter 'none')    (Use 'none' if your db server does not use a TCP socket to connect)    (If using a special PORT, you can specify it as HOST:PORT) ||
||Login ||db_login ||The database login    (Not used for SQLite.) ||
||Database Name ||db_password ||The database password for the above login<<BR>>    (Use 'none' if your db server does not use a TCP socket to connect.)    (Not used for SQLite.) ||
||Database Name ||db_database ||The name of the database that the calendar    tables reside in. ("intranet" in the [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#dbsetup|examples above]].)    For ODBC, this should be the DSN.    For SQLite, this is the filename that will be used. ||
||Connection Persistence ||db_persistent ||Enable use of persistent (pooled) database connections.    This should typically be enabled. This setting may be disabled for CGI installations. ||
||Database Cache Directory ||db_cachedir ||To enable caching of database query results, enter a writable directory. This      directory should NOT be in the webserver document path to prevent unauthorized      access to cached  sensitive data such as passwords hashes and private event      details.     To disable this feature, leave this field blank.     However, this feature is recommended to enhance your system's performance. ||




Click the Test Settings button to perform the test your settings.

Click the Create button to have the installation    script create your database. This option will only be available for fully    supported database types. Retest your database settings if prompted to do    so.
{{{
TIP:
If this step does not complete    properly, then you will have to manually create your database. Detailed instructions    can be found in [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#appendixB|Appendix B]]. Then rerun the installation    script to continue the installation.
}}}
=== WebCalendar Installation Wizard: STEP 3 ===
In this section we will perform the required database changes to  bring your database up to the required level If you are using a fully  supported database, this step will be performed automatically for you If  not, the required SQL can be displayed and you should be able to cut  & paste it into your database server query window.

'''Database Status'''

This should display the current installed version of WebCalendar as well as the version being installed.

'''The following database actions are required'''

If using a fully supported database type, clicking the '''Update Database''' button  will create any tables required for this installation or upgrade. If this feature is  not supported, then this button will not be available.

The '''Display Required SQL''' button will always be available   and if clicked will display the required SQL text that can then be cut & pasted   into the SQL query window of your database program. If using the approach,   please rerun the installation script after completing your required database   updates.
{{{
TIP:
If this step does not complete properly,    then you will have to manually create your database tables. Detailed instructions    can be found in [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#appendixF|Appendix F]]. Then rerun the installation    script to continue the  installation.
}}}
=== WebCalendar Installation Wizard: STEP 4 ===
'''Timezone Conversion'''

If upgrading WebCalendar from a previous version, you may be prompted  to perform a Timezone Conversion. WebCalendar version 1.0.4 and earlier  stored event date and time in the server's timezone. As of v1.1.0, all event date and time information is stored as  UTC time. So, a one-time conversion of existing events will required to  prevent event displayed times from appearing incorrectly.

'''Application Settings'''

Create Default Admin Account: If checked, a DEFAULT ADMINISTRATOR account will be created with username:admin password:admin

Application Name: The default value '''Title''' will be translated into  '''WebCalendar''' into the user's language. Any other entry will not be translated unless entries are made into the appropriate language.txt files.

Server URL: This should be the full URL to access your WebCalendar  installation.The trailing "/" is required for proper operation. If this  field is left blank, WebCalendar will calculate the proper value and store it in the database.

User Authentication: You can configure the calendar to run in single-user[[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#g_singleuser|*]] mode or  multi-user[[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#g_multiuser|*]] mode. If this is your first time using the  calendar, it's easier to try single-user. You can always switch to multi-user  later. Leave {{{single_user}}} set to "false" (the default) for multi-user or set  it to "true" and set the value of {{{single_user_login}}} to a login name of your liking  to set the system to single-user mode. (And be sure to set the value of  {{{single_user_login}}} to the login that you would choose if you decide to switch  to multi-user mode some day.)

Note: If you do decide to switch from  single-user mode to multi-user mode, make sure you add in a user to the  system for the login you set the   {{{single_user_login}}} variable to. You will need to do this via the   database (mysql, sqlplus, etc.)  Look in the {{{tables-mysql.sql}}}   (or {{{tables-oracle.sql}}}, etc.) to see the example of adding in the "admin" user.

If you are setting up a multi-user calendar, you will need to choose   how your users are authenticated. You must change the settings of   {{{use_http_auth}}} and {{{user_inc}}}   to setup which authentication method to use.

You currently have five choices:

 1. Web-based authentication (login/passwords verified in the WebCalendar database):<<BR>>    {{{use_http_auth = false}}}<<BR>>    {{{user_inc = user.php}}}
 1. HTTP-based authentication (login/passwords verified by the web server):<<BR>>    {{{use_http_auth = true}}}<<BR>>    {{{user_inc = user.php}}}<<BR>>    ... and don't forget to setup your web server to handle user    authentication.<<BR>>           Note: In order to use HTTP-based authentication,     PHP must be setup as a module for your server rather than a CGI.
 1. NIS-based authentication (login/passwords verified by NIS):<<BR>>    {{{use_http_auth = false}}}<<BR>>    {{{user_inc = user-nis.php}}}<<BR>>    Additional configuration settings will need to be set    in {{{includes/user-nis.php}}}.
 1. LDAP-based authentication (login/passwords verified by LDAP server):<<BR>>    {{{use_http_auth = false}}}<<BR>>    {{{user_inc = user-ldap.php}}}<<BR>>    Additional configuration settings will need to be set    in {{{includes/user-ldap.php}}}.
 1. IMAP-based authentication (login/passwords verified by IMAP server):<<BR>>    {{{use_http_auth = false}}}<<BR>>    {{{user_inc = user-imap.php}}}<<BR>>    Additional configuration settings will need to be set    in {{{includes/user-imap.php}}}.

Read-Only: Determines whether events can be added to your WebCalendar.

Environment: Normally set this '''Production'''. Development will allow more verbose sql logging and should not be used unless needed.

After saving your settings, you should be provided with a '''Launch WebCalendar'''  button that will open WebCalendar in a separate browser window.

'''Congratulations! You have successfully installed WebCalendar on your system.'''

Keep in mind that if you want to use reminders, you will need to   setup the {{{send_reminders.php}}} script (see below) and keep your admin   setting for "Email enabled" set to "Yes" on the admin settings page.

At this point, your WebCalendar installation should be up and running.   To access WebCalendar open up your favorite web browser and   type in the URL. The URL will depend on where you installed WebCalendar.
{{{
TIP:
 On a multi-user system, the only user account created  during installation has the username of "admin" and a password of "admin". You should create  a new admin account and delete this one for security reasons.
}}}
[[#|↑ top]]

----
== Configuring as Event Calendar ==
If you intend to use WebCalendar as an event calendar, you will   need to enable "Public Access" in the System Settings with the following   steps:

 * Login to WebCalendar as an administrator (the default username is         'admin' and password 'admin').
 * Click on the "Admin" link at the bottom of any page.
 * Click on the "System Settings" button.
 * Click on the "Public Access" tab.
 * Select "Yes" for "Allow public access".

This will allow users to access WebCalendar without a username and password. Only events that are created with the "Public User" as an event participant will show up in your event calendar for users who have not logged in. If events are not showing up on your public calendar, make sure the events have the "Public User" as a participant and the event has been approved.

[[#|↑ top]]

----
== Creating Users ==
After logging in as an admin user (the initial username is "admin" with   password "admin"), you will see a common set of links at the bottom   of each page. Follow these steps to create a new user:

 1. Login to WebCalendar as an admin user
 1. Click on the "Admin" link at the bottom of any WebCalendar page
 1. Click on the "Users" button
 1. Click on the "Add New User" link
 1. Fill out the form with details for the new user (email address is optional)
 1. Click on the "Save" button
{{{
TIP:
 On a single-user system, you do not need to create any users.

TIP:
 For an event calendar, you do not need to create a user for the "public user".
}}}
[[#|↑ top]]

----
== Setting Up Email Reminders ==
PHP does not come with a utility for executing time-based jobs.   So, in order to check periodically for email reminders, a shell   script was written in PHP. You will need two things to get this working:

 1. You should have a version of PHP built as a CGI (so that you can run     php from the command line). This does not mean you must build all     of PHP as a CGI. You can still build PHP as a module for your web   server and then build the CGI-based PHP later.<<BR>>     Note: Many Linux distributions and some Windows LAMP packages     come with the PHP built for CGI.
 1. You must setup cron (on Linux/UNIX) or something like cron for Windows   to run the {{{send_reminders.php}}} script periodically.

Building PHP as a CGI is outside the scope of these instructions. But,   if you read the PHP instructions, you'll see that the default build   settings will build the CGI-based PHP. If you really can't do this   (perhaps you don't have permission to install anything new on the   system), skip down a couple of paragraphs to an alternate solution   that does not require PHP CGI.

For Linux/UNIX users, add the following line to the crontab entry of   a user. (This would be the same user that the web server   process runs as.)

{{{
1 * * * * cd /some/directory/webcalendar/tools; ./send_reminders.php
}}}
Of course, replace the directory location to wherever the   {{{send_reminders.php}}} file can be found. If you moved this out of the   tools directory (which is recommended for security reasons),   be sure to update {{{send_reminders.php}}} since it needs   to know where to find other WebCalendar files.

If you cannot setup PHP as a CGI or have no idea how, you can leave   {{{send_reminders.php}}} in its current location and access it via a URL.   IMHO, this is not the best choice, but it still works. Setup a cron   job to access the URL. For Linux/UNIX users, add the following line to   the crontab entry of a user.

{{{
1 * * * * wget http://yourserverhere/webcalendardirectoryhere/tools/send_reminders.php > /dev/null
}}}
Some users have reported the following works better for their configuration.

{{{
1 * * * * wget -q -O /dev/null http://yourserverhere/webcalendardirectoryhere/tools/send_reminders.php
}}}
You should test this from the command line first to make sure your setup is   correct. If you do not have {{{wget}}} installed on your system, you can use   any tool (lynx, perl script, etc.) that is capable of making an HTTP request for this.

[[#|↑ top]]

----
== System Settings ==
System Settings allows the administrator to control what features are available to users  as well as default values for certain features.

Many of these settings can be overridden by users in their Preferences (such    as color). These user configurable settings are identified by   '''''Italics'''''.

=== Settings ===
 Application Name:: Specifies the document title (typically displayed in the window title bar   of most browsers)
 Server URL:: Specifies the base URL of the calendar. This information is needed to   accurately include URLs in email messages (Notifications and Reminders).
 Home URL:: Specifies the URL to return to when exiting WebCalendar. If set, a new menu option will   become visible in the menu.
 Language:: Specifies the default language setting for all users.
 Server Timezone Selection:: Allows you to manually set your server's WebCalendar timezone selection. This has    no effect on the OS Timezone settings.
 Allow user to use themes:: Allows users to have access to pre-defined themes that can set color or user preference    settings. There are a few examples available in the {{{themes}}} folder.
 Themes:: The default theme that will be applied to all users unless they make their own    changes.
 Allow top menu:: Turns on the NEW menu system for all users.
 Date Selectors position:: Determines position of Date Pulldown Selectors. Either inside the NEW      menu or in their original location at the bottom of the calendar.
 Menu theme:: Theme to be used with NEW menu.
 Fonts:: Specifies your preferred font. Multiple font names should be comma-separated.
 Custom script/stylesheet:: Place any custom stylesheet information or JavaScript that will        be included on all WebCalendar pages.
 Custom header:: The custom header allows you to specify HTML to be placed after        the <body> tag but before any WebCalendar content and        included on all WebCalendar pages.
 Custom trailer:: The custom trailer allows you to specify HTML to be placed after        the WebCalendar content but before the </body> tag and        included on all WebCalendar pages.
 Allow external file for header/script/trailer:: If enabled, then the Custom header and Custom trailer settings        can specify a filename on the server rather than HTML.
 Allow user to override header/trailer:: If enabled, then users can add their own custom HTML for        both the header (top of the page) and trailer (bottom of the page)        to customize the appearance of all pages.
 Preferred View:: Specify if users should see the day, week, month, or year after logging in.
 Display small months:: Specifies whether small months are included in month, week, day views.
 Display weekends:: Specifies whether weekends are included in month, week, day, and views.
 Display all days in month view:: Specifies whether the complete month grid is filled with days from previous and    next month.
 Display days with events in bold in month and year views:: Highlights and bolds the text for days containing events in the mini-calendars    used in day, month and year calendars.
 Display description in printer day view:: If enabled, then the "printer friendly" view of the day view will include full event     descriptions rather than just the event name.
 Display Common Use Date/Times as GMT:: Sets the timezone to used for General Purpose dates. Either GMT of the Server    timezone will be selected.
 Date format:: Specifies the default format for displaying dates. These are defined in      file    {{{includes/date_formats.php}}} and if '''LANGUAGE DEFINED''' is selected    then the format will be adjusted based on the format in the user's language.txt file.
 Time format:: Specifies the default time format as either 12-hour (3:45pm) or 24-hour (15:14)
 Display 00 minutes always:: Specifies whether times on the hour ( 10:00 ) are displayed with trailing 00.
 Entry interval:: Specify the default options for entry start and stop times. This values will also   determine the display resolution of timebar views as well as user availability.
 Time interval:: Specify the default number of minutes each time block represents in the day and   week display
 Auto-refresh calendars:: If set to "yes," the day, week, and month pages will automatically   reload after a specified duration
 Auto-refresh time:: Specifies how long to wait before the auto-refresh should force a page   to be reloaded
 Require event approvals:: If enabled, then events added to one user's calendar by another       user will require approval.
 Display unapproved:: Specifies whether events that have been added to a calendar but not yet   approved should display on the calendar (in a different color)
 Display week number:: Specifies whether the week number should be displayed in month and week views
 Week starts on:: Specifies if week start on Sunday or Monday
 Work hours:: Specifies the default time range to display in day and week views
 Disable Pop-Ups:: Determines whether event pop-ups are displayed.
 Disable Location field:: Determines whether to use the Location field for events.
 Disable Priority field:: If enabled, the Priority field will not be used
 Disable Access field:: If enabled, the Access field will not be used
 Disable Participants field:: If enabled, the Participants field will not be used, and users will not be   able to add events to any calendar other than their own.
 Disable Repeating field:: If enabled, users will not be able to create repeating events
 Display Site Extras in popup:: If enabled, custom event fields configured in {{{site_extras.php}}}       will appear in the mouse-over popups containing additional       event information.
 Display Participants in popup:: If enabled, event participants will be included in popup details.
 Allow HTML in Description:: Allow the use of HTML in the description field when creating  events. In addition, with the addition of either of two optional  packages (HTMLArea or FCKEditor), you can have a WYSIWYG interface.  These packages are available on [[http://www.k5n.us/webcalendar.php?topic=Add-Ons|WebCalendar's Home Page]]
 Allow viewing other user's calendars:: If enabled, users will be able to view the calendar of another user
 Include add event link in views:: If enabled, Views will include a link to quickly add events to the   specified user's calendar.
 Remember last login:: If enabled, when a returning calendar user reaches the login  page, their login name will be pre-filled with the last login username  that they entered. (The password field will still be blank.)
 Check for event conflicts:: Specifies if the system should check for scheduling conflicts when a user adds or updates an event.
 Conflict checking months:: If conflict checking is enabled, this specifies how many months  past the initial date the system will check for conflicts when a user  adds or updates a repeating event.
 Allow users to override conflicts:: If enabled, users will be warned when there is an event conflict  and be presented with the option of scheduling the event anyhow.
 Limit number of timed events per day:: If enabled, users can be limited to a specific number of timed events per day
 Maximum timed events per day:: Specifies that maximum number of events that can be scheduled in one day of any one user.
 Specify timed event length by:: Allows you to configure event lengths to be specified        by either duration or end time.
 Brief Description Length:: Specifies the maximum numbers of characters to display in calendar views.
 Display Lunar Phases in month view:: Show Lunar Calendar icons in month view. This will not use existing event space.
 Disable Cross-Day Events:: Determines whether to display events that cross user's day boundary as      multiple  events.

=== Public Access ===
 Allow public access:: If enabled, anonymous users will be able to view the public access calendar without logging in.
 Public access visible by default:: If enabled, user's can go to the public access page without having to select it.
 Public access is default participant:: If enabled, public access will be added to every event  created.
 Public access can view other users:: If enabled, public access user's will be able to see the calendar of other WebCalendar users. Use with caution!
 Public access can add events:: If enabled, anonymous users will be able to submit new events
 Public access new events require approval:: If enabled, events submitted to the public access calendar (by  anonymous users) will require approval by an admin user. If not enabled,  then new events will appear on the public access calendar as soon as  they are submitted.
  Public access can view participants:: If enabled, public access users can view the names of other participants for any event that public access is a participant. Use with caution!
 Override event name/description for public access:: If enabled, the value of the following setting will be used to mask the name and description of events.
 Text to display to public access:: Text to display as explained above.

=== User Access Control ===
 User Access Control enabled:: User Access Control provides more advanced control of        user access permissions.        If enabled, a "User Access Control" button will appear on the    main Admin page and/or on the Settings menu of the NEW top menu. This page    will allow you to configure:

::

 * which functions in the system each user can access
 * which user calendars each user can access            (user A can edit events on user B's calendar, etc.)

=== Groups ===
 Groups enabled:: Specifies if group features should be enabled
 User sees only his group:: If enabled, users will be unaware of any users that are not in the same group as the user.

=== NonUser Calendars ===
 Nonuser Calendars Enabled:: If enabled, you may create "nonuser calendars" in WebCalendar.      A nonuser calendar is a calendar without a user login. It can      be used to manage a resource like a conference room's availability.      Or it can be used to place common events (like holidays) that multiple      users may want to access as a layer.      You will need to enable nonuser calendars in order to subscribe      to remote iCalendar URLs from within WebCalendar (allowing the events      from an iCalendar or hCalendar file elsewhere on the net to appear in your      WebCalendar calendar.)
 Display in participants list at:: If enabled, then nonuser calendars can be selected as        participants to events.

=== Other ===
 Reports enabled:: If enabled, user's will be able to create and generate reports.
 Allow remote subscriptions:: If enabled, user's will be able enable their own subscription  settings. There are currently two main ways to publish events with  WebCalendar.

::

 * '''publish.php''' will provide outbound publishing to a calendar client
 * '''icalclient.php''' will provide two-way full synchronization between WebCalendar and another calendar client such as Sunbird, Apple iCal.       (PHP 4.3+ required)

 Allow remote calendars:: If enabled, user's will be able to add a special type Non-User Calendar      that includes a URL that point to the remote location. The user can manually      synchronize the remote calendars if desired. In addition, {{{tool/reload_remotes.php}}}      can be set up as a cron job to automatically reload all remote calendars      as desired. Currently both iCalendar and hCalendar calendars can be consumed      by WebCalendar. The  calendar type should be automatically detected based      on the URL and calendar content. Users will need to create a layer to make      a remote calendar visible on their calendars, just like regular Non-User      Calendars.
 Enable RSS feed:: If enabled, a link is added to all pages that permits some browsers to      create a persistent RSS feed to that page. Currently, only Firefox and IE      7.0 supports this functionality. Also, the setting will enable the use of      the rss.php script generate RSS feeds to RSS clients. User's will also be      required to enable this feature in their respective preference settings.
 Categories enabled:: Specifies if category features should be enabled
 Category Icon Upload enabled:: Specifies if category icons can be uploaded by users. This requires that      a folder named {{{icons}}} exist in the WebCalendar directory.
 Display small task list:: If enabled, a small task window will be displayed in the month  and day calendars. These tasks are compatible with the VTODO component  of RFC2445 (iCalendar)
 Display tasks in Calendars:: If enabled, tasks are displayed along with events in calendars.  The date they appear is the due date but if the due date has passed  without the task being completed, the task will shift to the current  day.
 Allow external users:: If enabled, the create/edit event page will contain a text area to include   the names (and optional email address) of event participants that are not calendar users.
 External users can receive email notifications:: If enabled, event participants entered into the External  Participants area will receive email notifications at the same time as  calendar users (if an email address was specified for the External  Participant).
 External users can receive email reminders:: If enabled, event participants entered into the External  Participants area will receive email reminders at the same time as  calendar users (if an email address was specified for the External  Participant).
 Allow self-registration:: If enabled, new users are permitted to setup their own accounts and log into WebCalendar without admin intervention. Use with caution!
 Restrict self-registration to blacklist:: If enabled, admin can configure the includes/blacklist.php to  restrict or permit self-registration based on the user's IP. This will  restrict access to existing users or new user's set up by admin. Details  and examples are available in the top of the file.
 Generate passwords and send to new users:: If enabled, self-registration user's will be emailed a randomly  generated password that they can then use to access WebCalendar  normally. Hopefully, this will prevent some spammers and hackers from  misusing the self-registration process.
 Allow file attachments to events:: If enabled, attachments can be uploaded and associated with events. The      attachments are stored in the database, so care should be taken if performance      is an issue. Additionally, permission may extended to participants and/or      anyone.
 Allow comments to events:: If enabled, comments can be associated with events. Additionally,    permission may extended to participants and/or anyone.

=== Email ===
 Email enabled:: Specifies if email functionality should be enabled. If set to "No," then no email messages will be sent at any time.
 Default sender address:: Specifies the email originator to use when the system sends out email Notifications and Reminders
 Email Mailer:: Specifies the email program to use. (PHP mail, SMTP, or sendmail)
 SMTP Host name(s):: Specifies the SMTP server name(s) or IP(s) if SMTP is selected above.
 SMTP Port Number:: Specifies the SMTP port number if SMTP is selected above. Default is 25.
 SMTP Authentication:: Specifies whether authentication is required if SMTP is selected above. Additionally,    '''SMTP Username''' and '''SMTP Password''' will be required.
 Event reminders:: Specifies if email reminders for events that include a reminder should be sent
 Events added to my calendar:: Specifies if the system should send email when an event is added
 Events updated on my calendar:: Specifies if the system should send email when an event is updated
 Events removed from my calendar:: Specifies if the system should send email when an event is deleted
 Event rejected by participant:: Specifies if the system should send email when a participant to an event rejects the event

=== Colors ===
 Allow user to customize colors:: Specifies whether color settings should be available to users in their Preferences
  Enable gradient images for background colors:: If enabled, the background of calendar table cells will be  rendered with a gradient image based on the background color selected  for that cell type. This option requires that php be compiled with the '''gd module'''.
 Document background:: Specifies the background color of all pages
 Document title:: Specifies the color of page title on each page
 Document text:: Specifies the default text color on each page
 My event text:: Specifies the default text color for the user's events
 Table grid color:: Specifies color of the lines that make HTML table grids on each page
 Table header background:: Specifies the default background for the heading of any HTML table
 Table header text:: Specifies the default text color for the heading of any HTML table
 Table cell background:: Specifies the background color for table cells
 Table cell background for current day:: Specifies the background color for the table cell containing the current        date ('''today''')
 Table cell background for days with events:: Specifies the background color of table cells containing events. This      setting will not override the '''today''' setting and      if identical to '''today''', will be ignored.
 Table cell background for weekend:: Specifies the background color for table cells that represent a Saturday or Sunday
 Table cell background for other month:: Specifies the background color for table cells of other month days.
 Week number color:: Specifies the text color for week numbers (if enabled)
 Event popup background:: Specifies the background color of event popup areas
 Event popup text:: Specifies the text color of event popup areas

[[#|↑ top]]

----
== Custom Event Fields ==
You may want to customize the event-specific fields found in   the {{{includes/site_extras.php}}} field.   If this is your first time using the calendar, you can skip   this step and come back later since this step   is ''optional''.

You can use this feature to add extra   fields to your calendar events. For example, you can add a URL,   a contact email address, or a pulldown containing predefined attributes.

When defining a new custom field, the following types listed below   are available. "Arg 1" and "Arg 2" have different meaning depending on the type   of field. In some cases, "Arg 1" and "Arg 2" are not used.
||<tablewidth="100%" tablestyle="">Type ||Description ||Arg 1 ||Arg 2 ||
||{{{EXTRA_TEXT}}} ||Allows the user to enter a single line of text ||Specifies the size of the text form element as it would appear in the     following ("NN" would be replaced with Arg 1):<<BR>>        {{{<input size="NN" ...}}} ||N/A ||
||{{{EXTRA_MULTILINETEXT}}} ||Allows the user to enter multiple lines of text ||Specifies how many characters wide the textform element should be as it     would appear in the following ("NN" would be replaced with Arg 1):<<BR>>        {{{<textarea cols="NN" ...}}} ||Specifies how many lines long the textform element should be as it would     appear in the following ("NN" would be replaced with Arg 1):<<BR>>        {{{<textarea rows="NN" ...}}} ||
||{{{EXTRA_URL}}} ||Allows the user to enter a single line of text and will be displayed as a     link when viewed ||N/A ||N/A ||
||{{{EXTRA_DATE}}} ||Allows the user to select a date using the standard date selection form     elements ||N/A ||N/A ||
||{{{EXTRA_EMAIL}}} ||Allows the user to enter a single line of text and will be displayed as     a mailto URL link ||N/A ||N/A ||
||{{{EXTRA_USER}}} ||Allows selection of a WebCalendar user from a pulldown list ||N/A ||N/A ||
||{{{EXTRA_RADIO}}} ||Allows selection of a single item from a group of radio buttons ||Specifies the list of available options using the PHP {{{array}}} ||The default item that will be selected (from list above) ||
||{{{EXTRA_SELECTION_LIST}}} ||Presents the user with a selection list (single selection) to choose from ||Specifies the list of available options using the PHP {{{array}}}     function ||0 indicates single selection, 1 indicates multiple selection and     also the maximum size ||
||{{{EXTRA_CHECKBOX}}} ||Will display a checkbox control ||N/A ||N/A ||




 . [[#|↑ top]]

----
== Frequently Asked Questions ==
 How do I setup PHP, MySQL and Apache on Windows?:: The easiest way to do this is to try one of the prepackaged bundles that will install all of these for you:
  * [[http://www.apachefriends.org/en/xampp.html|XAMPP]] [[http://www.apachefriends.org/en/xampp.html|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]
  * [[http://www.foxserv.net/|FoxServ]] [[http://www.foxserv.net|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]
  * [[http://www.sokkit.net/|Sokkit]] [[http://www.sokkit.net|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]] (formerly PHPTriad)
 How do I setup PHP, MySQL and Apache on Mac OS X?:: You can use the built-in Apache/PHP installed on Mac OS X.        However, this does not inlude MySQL.  An easier way to        setup WebCalendar would be to use one of the following        packages:
  * [[http://www.apachefriends.org/en/xampp.html|XAMPP]] [[http://www.apachefriends.org/en/xampp.html|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]
 How do I setup PHP, MySQL and Apache on UNIX/Linux?:: There are many online instructions on how to do this. Here are a few:
  * [[http://www.onlamp.com/pub/a/php/2000/11/17/php_admin.html|ONLamp.com: Basic Installation of PHP on a Unix System]] [[http://www.onlamp.com/pub/a/php/2000/11/17/php_admin.html|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]
  * [[http://www.devshed.com/Server_Side/PHP/SoothinglySeamless/|Developer Shed: The Soothingly Seamless Setup of Apache, SSL, MySQL and PHP]] [[http://www.devshed.com/Server_Side/PHP/SoothinglySeamless/|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]
  * [[http://www.linuxhelp.net/guides/lamp/|Linux Help: LAMP Guide]] [[http://www.linuxhelp.net/guides/lamp/|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]
  * [[http://www.php.net/manual/en/installation.php|PHP.net]] [[http://www.php.net/manual/en/installation.php|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]
 I've finished the install. What is the login to WebCalendar?:: After the initial creation of the database tables, there will     be a single user account created with the username "admin" and     the password set to "admin" as well.     <<BR>>Note: This account is intended to get your started.     You should create a new admin account and delete this one.
 I want to use WebCalendar as an events calendar for     my organization. How do I set it up to do this?:: You will want to setup WebCalendar to use public access.
  1. Setup your {{{settings.php}}} file as a multi-user       system (see [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#appsetup|instructions]]).
  1. In System Settings, set "Allow public access" to "Yes."
  1. If you want people to be able to submit new events       through public access, set "Public access can add events"       to "Yes."
  1. If you set "Public access can add events" to "Yes",       set the setting for "Public access new events       require approval" to your liking. If you set this to "Yes,"       an admin user will need to approve any new events before       they will appear on the public access calendar.
  1. Login using one of the accounts you have setup.        Add a new event, and select "Public User" as one of the        participants.
  1. If you have enabled "Require event approvals" in your        System Settings, then you will need to approve the new        event. Choose the "Unapproved Events" at the bottom        of any page (you must be an admin user to access this).        You will be presented with a list of unapproved events        (for both the current user and for the Public User account).        Approve the new event for the Public User.
  1. Go to the Login page. You will see a "Access public calendar"        link that will bring you to the calendar for public access.
  1. By default, the index.php page should send users to        the public calendar.
 How can I add holidays to the calendar?:: There is no built-in support for holidays because it is  not necessary. You can add holidays into WebCalendar by importing  one of the many iCalendar holiday files that are freely available.  A good resource for free iCalendar files is  [[http://www.icalshare.com|iCalShare]].  For example, the U.S. holiday iCalendar file can be downloaded from:  http://icalshare.com/article.php?story=20020912105939521  If you don't want each individual user to have to import  the holiday calendar, you can use nonuser calendars.  First, make sure nonuser calendars are enabled in your  system settings. Then, from Admin->Users, you can  access nonuser calendars. Create a new nonuser calendar  (such as "usholidays"). Then, notify your users that they  can add this nonuser calendar as a layer to their own  calendar in order to see the holidays.
 Why are deleted events still present in the database?:: When you delete an event from your calendar, it is not     deleted from the database. Instead, it is marked as deleted.     This allows the system administrator access to information     even after it is deleted.
 Can I setup more than one public calendar?:: You cannot directly setup two public calendars. However,     there are two options for emulating this type of functionality:
  * You can [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-UserManual.html#categories|create global categories]] and point users to the calendar with only a certain category displayed.
  * You can setup multiple NonUser calendars and enable         public access viewing of other users' calendars.         You can then link directly to the calendar of one of         the NonUser calendars, or you can         [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-UserManual.html#views|setup a view]]         that contains the calendar of one or more of the NonUser calendars.
 How do I change the title "Public Access" at the top of  the calendar?:: In the file {{{translations/English-US.txt}}},   change the line that says "Public Access" to what you want   it to say on the right side.<<BR>><<BR>>Example:
 {{{
Public Access: Foobar Event Calendar
}}}
 Can I embed WebCalendar as a component on another web page?:: WebCalendar is meant to be run as a standalone web application.     You can customize the appearance of WebCalendar to match your existing     site.     To do this, In System Settings, set both "Custom header" and   "Custom trailer" to "Yes" and press the "Edit" button to enter the     header and trailer content.     <<BR>>     If you are looking to just include a list of upcoming events in one     of your web pages, you can use the {{{upcoming.php}}} page in     WebCalendar to do this. Edit the top of this file to configure options.     (These settings will likely move into System Settings in subsequent release.)     You can then use an {{{iframe}}} elsewhere on your web site as     in the example below:
 {{{
<iframe height="250" width="300" scrolling="yes" src="upcoming.php"></iframe>
}}}
  . Include this HTML anywhere on any of your pages.     By default, the events from the public calendar will be loaded (so     you must have "Public Access" enabled in System Settings).     You can override this with another user's calendar.     (See {{{upcoming.php}}} for instructions on this.)     <<BR>><<BR>>     If you would like to display a small calendar (like the ones on the month view), you can add the     following example. There are some configuration options available in the top of this file as well.     You can then use an {{{iframe}}} elsewhere on your web site as     in the example below:
 {{{
<iframe height="200" width="180" scrolling="yes" src="minical.php"></iframe>
}}}
 How do I customize the appearance of WebCalendar so that it matches      the rest of my site?:: You can customize the appearance of WebCalendar to match your existing     site.     To do this, In System Settings, set both "Custom header" and     "Custom trailer" to "Yes", and press the "Save" button.     Then click on the "Edit" button to enter your site content.     The custom header will be included after the document's HTML     <body> tag but before any WebCalendar content.     The custom trailer will be included after the WebCalendar content     and before the document's HTML </body> tag.     If you want to add JavaScript or Stylesheet data, you can use     the "Custom script/stylesheet" option on the same System Settings page.     Any content entered will be included within the document's HTML     <head> section.
 How do I add a new translation?:: It's a fairly simple process. If you've ever translated a C-based app      that used GNU's gettext tool, then you'll have no problem. The I18N      support was based on GNU's [[http://www.gnu.org/software/gettext/gettext.html|gettext]] [[http://www.gnu.org/software/gettext/gettext.html|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]. Here's what you need to do.
  1. look in the "translations" directory
  1. copy the {{{English-US.txt}}} file into what you'd like to call your        language data file. (e.g. {{{cp English-US.txt French.txt}}})
  1. Now translate all the text to the _right_ of the ": " into the        new language. Do ''not'' alter the text to the left of the ": ".
  1. When you're done making changes, move into the "tools" directory.        Run the {{{check_translation.pl}}} script on your new data file to make        sure you have all the needed translations.        (e.g. {{{perl check_translation.pl French}}})
  1. Add the new language to both the $languages array and the        $browser_languages arrays defined in {{{includes/translate.php}}}.
  1. Test it out...
  1. Post a copy of your translation (along with your changes to {{{includes/translate.php}}}) to the [[http://sourceforge.net/tracker/?group_id=3870&atid=303870|SourceForge Patches]] [[http://sourceforge.net/tracker/?group_id=3870&atid=303870|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]] for WebCalendar.
 How do I update an existing translation?:: Just open up the translation file in the {{{translations}}} directory      with your favorite text editor (like vi, vim, emacs, pico, Notepad, etc.).      In particular look for places where missing translations are indicated.      All missing translations should be marked with a   "<< MISSING >>" note.   and typically the English version of the translation will also be included on the following line (as in the example below):
 {{{
# << MISSING >>
# Edit:
#
}}}
 <<BR>>   For some text, an abbreviation may be used rather than the English text.   In those cases, the full text will be noted as in the following example:
 {{{
# << MISSING >>
# custom-script-help:
# English text: Allows entry of custom Javascript or stylesheet text that
# will be inserted into the HTML "head" section of every page.
#
}}}
 <<BR>>      When you're done making changes, move into the {{{tools}}} directory.        Run the {{{check_translation.pl}}} script on your new data file to make        sure you have all the needed translations:
 {{{
perl check_translation.pl French
}}}
  . Post a copy of your translation to the   [[http://sourceforge.net/tracker/?group_id=3870&atid=303870|SourceForge Patches]] [[http://sourceforge.net/tracker/?group_id=3870&atid=303870|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]] for WebCalendar.
 During Import, I receive this error: Unknown Timezone: XXXXX defaulting to GMT.:: This is a notice that your import file contained unknown timezone information.   WebCalendar will simply save the event or task as GMT. You are encouraged to   submit these notices as [[https://sourceforge.net/projects/webcalendar/|Bug Reports to SourceForge]], so that WebCalendar can improve its TZID support.

[[#|↑ top]]

----
== Troubleshooting ==
 I get error messages about undefined variables when I try to view any page.:: On newer versions of PHP, the default setting of PHP is to display     messages when an undefined variable is referenced. To prevent     these messages from being displayed, change the setting of {{{error_reporting}}}     in your {{{php.ini}}} file to be:
 {{{
error_reporting = E_ALL & ~E_NOTICE
}}}
  . Alternately, you can disable any error messages from being displayed     in the browser and have them logged to a file. (See the comments     included in the {{{php.ini}}} file for instructions on how to do this.)
 I receive an error message saying "Error removing temporary file" or "Cache error".:: This indicates that a file in your database cache directory could not be    deleted or created.    The quickest solution to this problem is to not use the database    cache.  Simply remote the {{{db_cachedir}}} line from    {{{includes/settings.php}}} with a simple text editor.    This error is most common when the {{{send_reminders.php}}} script    is run from a cron job under a different system account than the    PHP application server is running under.  This causes files created by    the {{{send_reminders.php}}} script to be owned by a different    user than normal, preventing the PHP application from being able to    remove the files when needed.
 I receive an error message saying "Failed opening 'includes/...' for inclusion ...":: You may have to modify the {{{include_path}}} setting        in your {{{php.ini}}} file. Most users do not need to do this,        but some PHP installs seem to require it. You will need to        add the WebCalendar directory to the list of directories        for {{{include_path}}}.
 I get an error message from PHP saying "Call to undefined function: ...":: This tells you that your version of PHP is missing something that     WebCalendar needs. If the function mentioned is a database login   function (''ociplogin'', ''mysql_pconnect'', ''ibase_connect'', ''pg_pconnect''),   then you probably do not have the needed support for your database   compiled into PHP.     If the function is not a database connect call, then check the     [[http://www.php.net/manual/en/|PHP manual]] [[http://www.php.net/manual/en/|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]     to see if the function requires a specific version of PHP. You     may have an out-dated version of PHP that requires upgrading.
 When I try and view certain pages, nothing happens for 30 seconds, then I get a time-out error.:: On slower or very busy servers, it can take some time for the server     to get all the events. Most PHP installations have a built-in timeout     out of 30 seconds and will interrupt any request that takes longer than     that. This is most likely to happen on the year-long custom report or     on the month view when layers are being used. If you have access,     you can increase the time-out value for PHP in the {{{php.ini}}}     file by changing the setting of the {{{max_execution_time}}} setting.
 I get an error message that says "Can't connect to local MySQL server through socket '/tmp/mysql.sock'.":: This is a PHP/MySQL configuration issue. The value of {{{mysql.default_socket}}}   in your {{{php.ini}}} file must match the value of {{{socket}}} in your   {{{my.cnf}}} file. Edit the {{{php.ini}}} file to fix this problem.
 I am not receiving any email messages from WebCalendar.:: WebCalendar sends two types of email messages:     Notifications[[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#g_notification|*]] and Reminders[[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#g_reminder|*]].   Check the following if you are not receiving any email:
  1. You have defined a valid SMTP server in your PHP configuration file     {{{php.ini}}}. (The setting is "SMTP" in the "mail_function" section.)
  1. In WebCalendar's System Settings, you have set the "Email Enabled" setting to "Yes".
  1. In WebCalendar's System Settings, make sure you have the "Default sender address"    to something.    <<BR>>Note:           Some mail system will reject mail that has a "From" address           that is a different domain from the originating SMTP server.           So, if your SMTP server is smtp.mydomain.com and your "Default sender address"    is calendar@someotherdomain.com , some mail systems may bounce the mail back.
  1. For a Notification, make sure you have the type of Notification            set to "Yes" in the user's Preferences.
  1. For a Reminder:
   * Make sure you have "Event reminders" set to "Yes" in the user's Preferences.
   * Make sure you have [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#reminders|setup a cron job]] to periodically       run the {{{send_reminders.php}}} script.
 Some of the pages are displaying text in English rather than <insert your language here>:: The translations have been submitted at various points of WebCalendar development.    Some have not been updated to include newer features.
 The text that I entered in the Custom Event Fields is not being translated   to different languages.:: You will need to add an entry in each of the translation files for any text you add    into the Custom Event Fields.
 How do I get the most recent version of WebCalendar?:: You can download the latest public release from SourceForge's   [[http://sourceforge.net/project/showfiles.php?group_id=3870|file list for WebCalendar]] [[http://sourceforge.net/project/showfiles.php?group_id=3870|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]].   <<BR>>     You can download the latest development code from the CVS server using   the [[http://sourceforge.net/cvs/?group_id=3870|instructions provided by SourceForge]] [[http://sourceforge.net/cvs/?group_id=3870|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]]. (You will need a CVS client to do this.)
 How do I install a patch file listed on SourceForge's     list of WebCalendar patches ?:: Most patches are distributed as context diffs. That means they were produced using the UNIX {{{diff}}} command with the {{{-C}}} option. The patches are intended to be used with the [[http://www.fsf.org/software/patch/patch.html|GNU patch]] [[http://www.fsf.org/software/patch/patch.html|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]] program. This program is standard on most Linux systems and can be obtained as part of the [[http://www.cygwin.com|Cygwin]] [[http://www.cygwin.com|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]] package for Windows. Mac OS X will have the patch program installed if they install the developer tools CD.
 I forgot/lost my admin password. How can I reset it?:: The easiest way is to admin a new admin user and then use that     new user to reset the password for your old admin account.     Assuming you have deleted the original 'admin' login, you can use     the following SQL to insert a new admin user into the database:
 {{{
INSERT INTO webcal_user ( cal_login, cal_passwd, cal_lastname,
cal_firstname, cal_is_admin ) VALUES
( 'admin', '21232f297a57a5a743894a0e4a801fc3', 'Administrator',
'Default', 'Y' );
}}}
  . This will add a user with login 'admin' and password 'admin' to the database.     If you still have a user named 'admin', then replace 'admin' in the above     SQL with a different username.
 I get a database error indicating table webcal_config does not exist.:: This is the first table that WebCalendar tries to access, so it    typically means one of the following:
  * You have not created the database tables as described in the instructions
  * You have the wrong database name specified in your     {{{includes/settings.php}}} file
 I get a database error from MySQL that says "Client does not support authentication protocol.":: From the MySQL 5.0 Reference Manual:
  . The [[http://dev.mysql.com/doc/refman/5.0/en/old-client.html|MySQL     Reference Manual]] has solutions for this problem.

[[#|↑ top]]

----
== Getting Help ==
Try the Help/Troubleshooting forum for WebCalendar, hosted at SourceForge.net:

{{{
}}}
If you encounter a bug, please check the   [[http://sourceforge.net/tracker/?group_id=3870&atid=103870|list of open and pending bugs]] [[http://sourceforge.net/tracker/?group_id=3870&atid=103870|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]].   If you do '''not''' see anything similar, submit a new bug.

[[#|↑ top]]


----
== Licensing ==
WebCalendar is distributed under the open source   [[http://www.gnu.org/licenses/gpl.html|GNU General Public License]] [[http://www.gnu.org/licenses/gpl.html|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]].   If you have questions about this license, please   read their [[http://www.gnu.org/licenses/gpl-faq.html|GPL FAQ]] [[http://www.gnu.org/licenses/gpl-faq.html|{{http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/newwin.gif|new|class="newwin"}}]].

[[#|↑ top]]

----
== Glossary ==
 Activity Log:: A summary of recent updates to calendar data
 Assistant:: A calendar user that has been designated by another calendar user     (the Boss) to help manage their calendar
 Boss:: A calendar user that has designated another calendar user     (the Assistant) to help manage his calendar
 External User:: A calendar participant that does not have a calendar user account
 Group:: A mechanism of dividing up a large set of users into smaller sets of users
 IMAP:: Internet Message Access Protocol, an optional method of user authentication.
 Layer:: A function that allows a user to overlay another user's calendar     on top of his own calendar so that the standard day, week and month     pages show both his own and the layered user's events
 LDAP:: LDAP (Lightweight Directory Access Protocol) is an Internet protocol     used to maintain user directories
 Multi-User Mode:: When WebCalendar is configured in Multi-User Mode,     there can be multiple user accounts, each with his     own calendar. Unless Public Access is enabled, all users     will be required to login before they can access the system.
 NIS:: NIS (Network Information Service) is a UNIX-based user authentication     system for managing user directories in a network
 NonUser Calendar:: A participant to a calendar event that is not a calendar user. This is typically used either as a resource (conference room,   laptop computer) that needs to be shared or as a shared calendar   that other users overlay onto their own calendar using layers (company-wide calendar,   holiday calendar, etc.)
 Notification:: An email message that is sent when an event is added, removed     or updated in the user's calendar by another user
 Preferred View:: The standard page (day, week, month or year) that will     be presented to the user after logging in     (set in user [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#pref|Preferences]])
 Public Access:: A [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#systemsettings|System Setting]] that will allow anonymous users     to access the calendar.     See the [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#samplepublic|simple instructions]] for     setting this up in the [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#faq|FAQ section]].     (Requires WebCalendar to be configured in Multi-User Mode).
 Reminder:: An email message that is sent before an event to remind     the participant
 Single-User Mode:: When WebCalendar is configured in Single-User Mode,     there is no concept of users. You will be managing a single     calendar and no login will be required.     Anyone accessing this calendar will have full privileges to     view, add, edit and delete all events.
 Time Interval:: The amount of time each "block" will represent in     either the day or week view     (set in user [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#pref|Preferences]])
 User Access Control (UAC):: The fully configurable feature to allow extremely granular    access control to user. This control applies to system options and    features as well as other user's calendars.
 View:: A customized page that presents the events of selected users
 Work Hours:: The default hours to show in the week and day view where     events are displayed in blocks of time (set in     user [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#pref|Preferences]])

[[#|↑ top]]

----

== Appendix A: Using phpMyAdmin ==
If you have [[http://www.phpmyadmin.net|phpMyAdmin]] installed and configured to manage your MySQL database, use the following steps to setup WebCalendar. (The following information is based on phpMyAdmin version 2.6.1.)

 1. On the initial phpMyAdmin page, under the "MySQL" heading, the first   option should be "Create new database."  Enter the name you have   chosen for the database and press the "Create" button.   (The default database name used by WebCalendar is "intranet".)   After pressing the "Create" button, it should say:   "Database <your database name here> has been created."
 1. Click on the home icon (the small house) in the left-side    navigation. This will bring you back to your phpMyAdmin home page.
 1. (Optional) Create new MySQL user. If you already have     a MySQL login that you would like to use, you can skip this step.
 1. * Click on the "Privileges" link under the "MySQL" heading.
  * Below any existing users listed, click on the link "Add a new User."
  * Fill in the details of your new database user.       The default username for WebCalendar is "webcalendar"       with a password of "webcal01".       Leave the "Host" field set to "Any host".
  * From the list of "Global privileges", be sure to select:       SELECT, INSERT, UPDATE, DELETE, FILE, CREATE, ALTER, INDEX, DROP
  * Click on the "Go" button.
  * You should see a page that says "You have added a new user."
 1. Click on the "Databases" tab at the top of the page.
 1. From the list of databases on the page, click on the name     of the database that you created.
 1. Click on the "SQL" tab at the top of the page.
 1. At the bottom of the page, there is an area to upload a SQL file.     Click on the "Browse" button and select the {{{tables-mysql.sql}}}     file in the WebCalendar toplevel directory.     Then, press the "Go" button.
 1. The top of the page should say "Your SQL-query has been executed     successfully."
 1. You have now finished creating the WebCalendar database tables.

[[#|↑ top]]

----
== Appendix B: Database Setup ==
There are three steps in setting up the database:

 1. Creating the database
 1. Creating the user
 1. Creating the required tables

Follow the steps outlined below for the database you are using.   When complete, a single user account will be created   with the '''login "admin" and password "admin"''', which you are encouraged   to use to create your own account.

Note: In the examples below, text in '''bold'''   represents text that you must type in.

Security: The default values for database, login, and   password ('''intranet''', '''webcalendar''', and '''webcal01''') are for demonstration purposes only   and should never be used in a production environment.

=== MySQL ===
The following will create a database named "intranet".

{{{
}}}
Next, create the database user account that will be used to access the database.

{{{
mysql>
mysql>
mysql>
}}}
If you will be accessing MySQL from a different machine than   the one running the web server, repeat the command above   and replace 'localhost' with the hostname of the other machine.

Create the calendar tables using the supplied {{{tables-mysql.sql}}} file:

{{{
}}}
In the above example, "intranet" is the name of your database.

Note: If you are using  [[http://www.phpmyadmin.net|phpMyAdmin]] to  manage your MySQL database, follow the  instructions in [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#appendixA|Appendix A]].

=== Oracle ===
The following will create a tablespace named "webcalendar".   From the command line, startup sqlplus and   issue the following command:

{{{
SQL>
}}}
Next, create the database user account that will be used to access the database.

{{{
SQL>
SQL>
SQL>
}}}
Create the calendar tables using the supplied {{{tables-oracle.sql}}} file:

{{{
SQL>
SQL>
}}}
=== PostgreSQL ===
The following will create a database named "webcalendar".   From the command line as the psql user: issue the following commands:

{{{
$
#
#
#
}}}
=== Interbase ===
The following will create a database named "WEBCAL.gdb".   From the command line, startup usql and   issue the following command:

{{{
}}}
Create the calendar tables using the supplied {{{tables-ibase.sql}}} file:

{{{
}}}
=== ODBC ===
Setup will depend on which database you are using.   When it comes time to create the tables,   the {{{tables-postgres.sql}}} file should work for most   databases.

=== MSSQL ===
Create a database, '''intranet,'''  and add a user, '''webcalendar''', to access this database. The user should be granted public, db_datareader,    and db_datawriter privileges. Open SQL Query Analyzer then open    the file {{{tables-postgres.sql}}}. Make sure you have identified '''intranet''' as the target database and Execute the contents of the sql file.

[[#|↑ top]]

== Appendix C: Setting Up Reminders on Windows ==
You have two options to choose from when setting up email    reminders on a Windows platform.    You can either install a cron package for Windows,    or you can use the Windows Task Scheduler.

=== Installing a Cron Package ===
If you install a cron package for Windows, you will need to setup to setup a cron job. (UNIX cron is a tool for used to run tasks as specified times anywhere from every minute to once a year.)

First, you should create a {{{sendreminders.bat}}} file that contains a single line:

{{{
C:\your\path\to\php.exe C:\your\path\to\webcalendar\tools\send_reminders.php
}}}
You can place this {{{sendreminders.bat}}} file anywhere on your file system.

Next, you need to setup the cron job. The crontab entry should look like the following (replace with the correct path to your newly created {{{sendreminders.bat}}} file):

{{{
1 * * * * C:\your\path\to\sendreminders.bat
}}}
The "1 * * * *" will tell the cron schedule to run this task at 1 after the hour all day long (12:01am, 1:01am, 2:01am, etc.) If you only want to run it once per day, you could use:

{{{
1 4 * * * C:\your\path\to\sendreminders.bat
}}}
This would tell the cron scheduler to run the task at 4:01am every day. For more information about the syntax of cron, check the documentation for the package you have installed or view the UNIX man page for crontab (like [[http://www.rt.com/man/crontab.5.html|this one]]).

There are many cron packages for Windows available.    Below is a list of packages to choose from.    (Note: use at your own risk. These links are provided    for information only.)

 * [[http://cronw.sourceforge.net/|CRONw]] (open source)
 * [[http://www.gold-software.com/VisualCron-review11507.htm|VisualCron]] (freeware)
 * [[http://www.nncron.ru|nnCron]] (freeware and commercial versions)
 * [[http://surguy.net/articles/icron.xml|iCron]]
 * [[http://p.clark.home.mindspring.com/jcron/index.html|jCron]] (freeware)

=== Using the Windows Task Schedule ===
Follow the steps listed below to setup a new task    in the Windows Task Scheduler.    (These instructions were created with Windows XP Professional.    Other versions of Windows might be different.)

 1. Create a new batch file called {{{sendreminders.bat}}}.     The contents of this file should be a single line:
 {{{
C:\your\path\to\php.exe C:\your\path\to\webcalendar\tools\send_reminders.php
}}}
 1. On Windows XP, go to Control Panel
 1. Double-click on "Scheduled Tasks."   This brings up a window that says "Scheduled Task Wizard"
 1. Click on "Browse..."
 1. Select the location of your newly created     {{{sendreminders.bat}}} file.
 1. Click on "Open"
 1. Change the name of the task. "WebCalendar Reminders" is a good name.
 1. Select how often to perform this task. Select "Daily."
 1. Click on "Next"
 1. Select the start time, then click "Next".     If you are planning on sending out reminders throughout the     day, pick a time shortly after midnight.
 1. Enter your user password as required. Click on "Next."
 1. Select the checkbox "Open advanced properties".
 1. Click on "Finish."
 1. Under the "Schedule" tab, click on "Advanced."
 1. Click on "Repeat Task" and fill in the details of how often      the job should run.      For example, you can set it to run "every 15 minutes", then      click on "until" and set that time to 11:30pm.

[[#|↑ top]]

----
== Appendix D: Displaying Upcoming Events on Your Site ==
If you would like to list upcoming events somewhere on a page on your site (someplace other than the WebCalendar pages), you can use the {{{upcoming.php}}} page to do this. This page is intended to be included in other pages using the {{{iframe}}} tag.

You may need to modify some of the variables near the top of {{{upcoming.php}}} with a text editor:
||<tablestyle="margin-left:20px;margin-right:20px;"style="vertical-align:top">$public_must_be_enabled ||Specifies if Public Access must be enabled in System Settings       for this page to be viewed.       <<BR>>Default setting: false ||
||<style="vertical-align:top">$display_link ||Specifies if events should have a link to the URL       within WebCalendar to view the event.       <<BR>>Default setting: true ||
||<style="vertical-align:top">$link_target ||Specifies the name of the window that be used for       the link target.       <<BR>>Default setting: _top ||
||<style="vertical-align:top">$numDays ||Specifies how many days of events should be listed.       <<BR>>Can override this by passing "num" in within the URL            with "upcoming.php?num=60"       <<BR>>Default setting: 30 ||
||<style="vertical-align:top">$maxEvents ||Specifies the maximum number of events to list.       <<BR>>Default setting: 10 ||
||<style="vertical-align:top">$username ||The login of the calendar to display upcoming events for.       If you want to see upcoming events for user login "joe",       then you would change this to "joe".       <<BR>>Default setting: __public__ (The public calendar) ||
||<style="vertical-align:top">$allow_user_override ||Specifies whether the calendar user can be specified in the URL       (in which case the $username setting will be ignored) using       a URL like "upcoming.php?user=joe".       <<BR>>Default setting: true ||
||<style="vertical-align:top">$load_layers ||Specifies if the calendar user's layers should also be       included in the upcoming events.       <<BR>>Note: Layers must be enabled in the user's preferences.       <<BR>>Default setting: true ||
||<style="vertical-align:top">$cat_id ||Specifies a category id to filter events on.       <<BR>>Note: Categories must be enabled in System Settings.       <<BR>>Default setting: (empty) ||




Below is an example of how you would include {{{upcoming.php}}}    in a simple HTML page.

{{{
<html><head><title>ACME Home Page</title></head>
<body>
<h1>Welcome to the ACME Home Page</h1>
<h2>News</h2>
<p>No news....</p>
<h2>Upcoming Events</h2>
<iframe src="upcoming.php" width="400" height="400" name="califrame"></iframe>
</body>
</html>
}}}
{{{
TIP :
  The [[http://www.w3schools.com/tags/tag_iframe.asp|W3 Schools]] site provides documentation   about the different options that you can use   with the {{{iframe}}} tag.
}}}
[[#|↑ top]]

----
== Appendix E: How To Configure for LDAP ==
Configuring WebCalendar to use an existing LDAP directory is fairly simple. It is know to work with OpenLDAP and Novell Edirectory and should work with other systems as well.

 . Note: If you use LDAP, the group functionality of WebCalendar does not yet work.

To configure WebCalendar to use LDAP, do the following:

 1. Verify that your PHP install supports LDAP.
 1. Configure {{{settings.php}}}
 1. Configure {{{includes/user-ldap.php}}}

The first step is to verify that your install of PHP supports LDAP. The  simplest way to do this is to view the output of phpinfo(). If you  can't easily call phpinfo() from another page, consider pasting the following  into a new page (e.g. {{{phpinfo.php}}}):

{{{
<html><head><title>PHP Info page</title></head>
<body>
<?php
  phpinfo()
?>
</body>
</html>
}}}
When you load the page in your browser, the resulting output will begin with  general PHP information and then include an alphabetical list of enabled PHP  modules. If you aren't sure where to put the file so you can load it via your  web server, place it in the webcalendar directory and add "phpinfo.php"  to the URL for your webcalendar install. If your PHP supports LDAP, it will be  listed as a module and version information will be given. If "ldap" is  not listed anywhere in the output of {{{phpinfo()}}}, then your PHP install  does not support LDAP. You may need to install additional packages, depending on  your OS and version; under Debian Linux, for example, the "php4"  package does not include support but, "php4-ldap" does.

The second step is to set WebCalendar to use LDAP authentication in {{{settings.php}}}. Instructions on to do this can be found in the [[http://webcalendar.cvs.sourceforge.net/viewvc/webcalendar/webcalendar/docs/WebCalendar-SysAdmin.html#appsetup|Application Installation]] section above. In summary, the following should be set:

 . {{{use_http_auth = false}}} {{{user_inc = user-ldap.php}}}

The next step is a little more work and involves editing the {{{includes/user-ldap.php}}} file with a text editor. You will have to set several configuration variables. If you don't know what to set the variables to, consult your LDAP system administrator. Documentation exists for all variables in the file. Finally, users have gotten LDAP working with OpenLDAP and Novell Edirectory by just changing the following:
||<tablestyle="margin-left:20px;margin-right:20px;"style="vertical-align:top">$ldap_server ||FQDN or IP address of the LDAP server (or localhost).       <<BR>>ex. 'ldapserver.company.com' ||
||<style="vertical-align:top">$ldap_base_dn ||Specifies the base DN used to search for users       <<BR>>ex. 'ou=people,dc=company,dc=com' or 'o=context,ou=subcontext' ||
||<style="vertical-align:top">$ldap_admin_group_name ||Specifies a group (complete DN) with admin rights for WebCalendar       <<BR>>You might have to create one in LDAP       <<BR>>ex. 'cn=it,ou=group,dc=company,dc=com' ||




=== Using SSL with LDAP ===
If the LDAP server will accept connections over SSL, simply add 'ldaps://' to the beginning of {{{$ldap_server}}}. Example: ldaps://ldapserver.company.com

=== Using TLS with LDAP ===
If the LDAP server is set up to use TLS,  set {{{$ldap_start_tls = true}}}.

=== Using LDAPv3 ===
If the LDAP server uses protocol version 3,  set {{{$set_ldap_version = true}}} and {{{$ldap_version = '3'}}}.

== Appendix F: Manual Installation ==
