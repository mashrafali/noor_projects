## page was renamed from Enterprise/IT/TechDocs/HowTo/Linux/Install_and_config_Moinmoin
## page was renamed from Enterprise/IT/Docs/HowTo/Linux/Install_and_config_Moinmoin
## page was renamed from Enterprise/IT/Docs/How_To/Linux/Install_and_config_Moinmoin
## page was renamed from Enterprise/IT/Docs/How_To/Linux/Install_config_Moinmoin2
{{{
Author       : Karim Farag
Version      : 1.0
Creation date: 2012-06-28
Latest update: 2013-11-25(Ayman Tohamy)
}}}
= Install and configure Moinmoin =
<<TableOfContents(4)>>

= Moinmoin =
'''Moinmoin''' is an advanced, easy to use and extensible [Wiki Engine] with a large community of users. It is about collaboration on easily editable web pages. <<BR>> '''Moinmoin''' is a wiki engine implemented in Python, initially based on the !PikiPiki wiki engine. The '''Moinmoin''' code is licensed under the GNU General Public License v2 <<BR>> Python is a dynamic object-oriented programming language and it is also often used as a scripting language <<BR>> If you want to learn more about wiki in general, first read about http://moinmo.in/WikiWikiWeb , then about http://moinmo.in/WhyWikiWorks and the http://moinmo.in/WikiNature . <<BR>>

= Installation =
== Installation Requirements ==
- Ubuntu server 11.4 - '''Moinmoin''' 1.9.3 - apache 2 Download '''Moinmoin''' 1.9.3 from the following link: <<BR>> http://static.moinmo.in/files/moin-1.9.3.tar.gz <<BR>>

{{{
sudo wget http://static.moinmo.in/files/moin-1.9.3.tar.gz
}}}
Install '''Moinmoin''' by following the guidance of the following link: <<BR>> http://moinmo.in/HowTo/UbuntuQuick <<BR>>

== Apache and WSGI module: ==
- The Apache HTTP Server, commonly referred to as Apache, Apache is web server software notable for playing a key role in the initial growth of the World Wide Web, it also supports a variety of features, many implemented as compiled modules which extend the core functionality. These can range from server-side programming language support to authentication schemes. Some common language interfaces support Perl, Python and PHP <<BR>> - The Web Server Gateway Interface (WSGI) defines a simple and universal interface between web servers and web applications or frameworks for the Python programming language. <<BR>> '''Moinmoin''' recommend using the Apache web server, with the recommended WSGI module so we will install <<BR>> To install both Apache and WSGI Module we write the following command: <<BR>>

{{{
sudo apt-get install apache2 libapache2-mod-wsgi
}}}
== Moinmoin Installation ==
Next step is unpack the downloaded files using the following command

{{{
sudo tar xvzf moin-1.9.3.tar.gz
}}}
Now we go to the location where we unpacked our files to install the '''Moinmoin''' and as it is written using python we will install with the following command

{{{
cd moin-1.9.3
sudo python setup.py install --force --prefix=/usr/local --record=install.log
}}}
Now we copy the configuration files to start configuring our wiki

{{{
cd /usr/local/share/moin
sudo cp server/moin.wsgi .
sudo cp config/wikiconfig.py .
}}}
== For the web server configuration ==
To edit the webserver configuration we add the following lines to the end of the configuration file of the apache web server this lines tells the server when a request comes asking for [http://servername/FrontPage]go to moin.wsgi which is a script, run this script and show the result, it also creates parameters which will be used during the process like user [www-data]

{{{
sudo vim /etc/apache2/apache2.conf
}}}
Add at the End of the file:

{{{
#  MoinMoin WSGI configuration
#
# you will invoke your moin wiki at the root url, like http://servername/FrontPage:
WSGIScriptAlias /   /usr/local/share/moin/moin.wsgi

# create some wsgi daemons - use these parameters for a simple setup
WSGIDaemonProcess moin user=www-data group=www-data processes=5 threads=10 maximum-requests=1000 umask=0007

# use the daemons we defined above to process requests!
WSGIProcessGroup moin
}}}
'''Save & close the file. '''

== Wsgi configuration ==
In this part we will define the path of the wikiconfig.py file for the WSGI module

{{{
sudo vim /usr/local/share/moin/moin.wsgi
}}}
Under the a2) paragraph we will add the path of the folder which contain the wikiconfig.py file by adding the following line:

{{{
sys.path.insert(0, '/usr/local/share/moin')
}}}
Save & close the file.

== Security permission setup ==
Now we will change the security permissions of the folder which contain the '''Moinmoin''' data so when the system run scripts it can be saved and edited the wiki data

{{{
cd /usr/local/share
sudo chown -R www-data:www-data moin
sudo chmod -R ug+rwX moin
sudo chmod -R o-rwx moin
}}}
To apply changes

{{{
sudo /etc/init.d/apache2 restart
}}}
= Configuration =
== Set your starting page ==
In this section we will modify the basic configuration in the wiki configuration file so we will open the configuration file using the following command

{{{
sudo vim /usr/local/share/moin/wikiconfig.py
}}}
In the user interface section, uncomment and modify the Welcome Front Page as:

{{{
page_front_page = u"FrontPage"
}}}
Last but not least, in the security section we will define the name of the superuser as following

{{{
superuser = [u"WikiAdmin", ]
Save & close the file.
}}}
== Moinmoin Access Control Lists ==
Access Control Lists (ACL's) allow you to control the permissions of a user or a group of users you can define permissions for the entire wiki, or for select pages of the wiki. <<BR>> Using ACL's, you can easily configure the wiki so that anonymous users can only read, but not edit pages. You can create pages that the general audience can't read until you're read to publish. You can set permissions so that only certain users are able to edit. <<BR>> To use ACLs, you need either access to the wikiconfig.py (for setting global ACLs), or the admin right on the specific page where you want to set (or change) ACLs. <<BR>> ACL consist of 2 Parts <<BR>> 1. Part that apply on entire wiki site <<BR>> 2. Part that apply on a certain Page <<BR>>

=== ACL that apply on the entire wiki site ===
These configuration items are used to set up ACLs on the entire wiki site, and are set in your wikiconfig.py

{{{
acl_hierarchic          False           True to use hierarchical ACLs
acl_rights_after        u''             ACL that is processed after the on-page/default ACL
acl_rights_before       u''             ACL that is processed before the on-page/default ACL
acl_rights_default      ...             ACL used if no ACL is specified on the page
acl_rights_valid        ...             Valid tokens for right sides of ACL entries
}}}
A part of our ACL configuration under the security partition of the wikiconfig.py, note that the syntax must be written exactly as shown below as this is python script:

{{{
# Security ----------------------------------------------------------
    # IMPORTANT: grant yourself admin rights! Replace Your Name with
    # your user name. See HelpOnAccessControlLists for more help.
    # All acl_rights_xxx options must use unicode [Unicode]
    acl_rights_before = u"WikiAdmin:read,write,delete,revert,admin"
    acl_rights_default = u'Known:read,write'
    acl_hierarchic = True
}}}
{{{
The first line means "before applying the local ACL of the page grant
WikiAdmin Read, write, delete and admin rights" The second line means that, by default, only known [logged in ] users are allowed to read or write
}}}
=== ACL Example: ===
 * '''Create IT group and grant it admin to all WIKI pages:'''

{{{
- vi /usr/local/share/moin/wikiconfig.py
}}}
{{{
acl_rights_before = u"WikiAdmin:read,write,delete,revert,admin ItGroup:read,write,delete,revert,admin"
acl_rights_default = u"All:read"
acl_hierarchic = True
}}}
 * Create Page for ItGroup <<BR>>
 {{{
--> login to http://wiki.noor.com/ItGroup
}}}

 * Edit Page TXT:

{{{
#acl
ItGroup:read,write
#format wiki
* karim.farrag
* ayman.tohamy
* ashraf.ali
* haitham.hassan
}}}
{{{
# sudo /etc/init.d/apache2 restart
}}}
=== ACL that apply on a certain Page ===
The configuration line should be wrote on as a comment on the page such as

{{{
#acl +All:read SomeUser:admin SomeGroup:read,write
}}}
where '''+All:read''' means that when any user requesting the '''read''' permission, will receive it.

and where '''!SomeUser''' is granted the '''admin''' permission, in all other cases it will be denied and processing will stop. (Different user or different permission requested).

A member of '''!SomeGroup''' is granted a permission to read and edit the content

3.3 Navigation bar: To customize the navigation bar to add NOOR business units, open the '''wikiconfig.py''' file and edit the '''nav''' section

{{{
sudo vim /usr/local/share/moin/wikiconfig.py
}}}
Add the following

{{{
navi_bar = [
        u'RecentChanges',
        u'FindPage'
        u' Corporate Services BU ',
        u' Consumer Services BU ',
        u' Infrastructure BU ',
        u' NOC ',
}}}
== Macros ==
Macros allow the insertion of system features into normal wiki pages; the only thing that makes those pages special is that they contain macros. If you edit pages like !RecentChanges or !SystemInfo by adding the syntax of the macro which is <<MacroName>> A lot of built in Macros are ready for being used in the following link: http://moinmo.in/HelpOnMacros - Macros can be useful for many things such as:

{{{
<<RecentChanges>>         a list of recently edited pages.
<<TitleIndex>>            an index of all page titles.
<<FullSearch>>            create a search box on the page, which search the page contents
<<AdvancedSearch>>        create an advanced search dialogue on the page.
<<SystemInfo>>            info on the wiki, the python interpreter and the host system.
}}}
=== To create a new macro userlist macro for example: ===
1. Save the syntax into a .PY file. 2. Create new file named '''UserList.py''' and paste below script inside it under the following directories: - /usr/local/lib/python2.7/dist-packages/MoinMoin/macro - /usr/lib/python2.7/ 3. The file content is shown below:

{{{
#so-8859-1 -*-
"""
     MoinMoin - User account Listing

     (mostly copied from admin.do_user_browser)
     @copyright: 2001-2004 Juergen Hermann <jh@web.de>,
                 2003-2007 MoinMoin:ThomasWaldmann,
                 2007-2010 MoinMoin:ReimarBauer,
     @license: GNU GPL, see COPYING for details.
"""

from MoinMoin import user, wikiutil
from MoinMoin.util.dataset import TupleDataset, Column
from MoinMoin.widget.browser import DataBrowserWidget
from MoinMoin.Page import Page


        u' Infrastructure BU ',
        u' NOC ',

def macro_UserList(macro):
     """ macro to list users. """
     request = macro.request
     _ = request.getText

     data = TupleDataset()
     data.columns = []
     data.columns.extend([
         Column('name', label=_('Username')),
         Column('email', label=_('Email')),
         Column('groups', label=_('Member of Groups')),
         Column('language', label=_('Language'), autofilter=1),
     ])
     isgroup = request.cfg.cache.page_group_regexact.search
     groupnames = request.rootpage.getPageList(user='', filter=isgroup)

     # Iterate over users
     for uid in user.getUserList(request):
         account = user.User(request, uid)
         # don't offer groupnames to users which aren't allowed to read them
         grouppage_links = ', '.join([Page(request, groupname).link_to(request)
                                      for groupname in groupnames
                                      if account.name in request.groups.get(groupname) and request.user.may.read(groupname)])

         userhomepage = Page(request, account.name)
         if userhomepage.exists():
             namelink = userhomepage.link_to(request)

else:
             namelink = wikiutil.escape(account.name)

         if account.disabled:
             enable_disable_link = request.page.link_to(
                                     request, text=_('Enable user'),
                                     querystr={"action": "userprofile",
                                               "name": account.name,
                                               "key": "disabled",
                                               "val": "0",
                                              },
                                     rel='nofollow')
             namelink += " (%s)" % _("disabled")
         else:
             enable_disable_link = request.page.link_to(
                                     request, text=_('Disable user'),
                                     querystr={"action": "userprofile",
                                               "name": account.name,
                                               "key": "disabled",
                                               "val": "1",
                                              },
                                     rel='nofollow')
if account.email:
             email_link = (request.formatter.url(1, 'mailto:' + account.email, css='mailto') +
                           request.formatter.text(account.email) +
                           request.formatter.url(0))

<<RecentChanges>>



         else:
             email_link = ''

         # language defined in settings or default language
         language = account.language or request.cfg.language_default

         data.addRow((
             (request.formatter.rawHTML(namelink)),
             email_link,
             request.formatter.rawHTML(grouppage_links),
             (language, language)
         ))

     if data:
         browser = DataBrowserWidget(request)
         browser.setData(data, sort_columns=[0])
return browser.render(method="GET")

     # No data
     return ''
}}}
=== To create a new macro Color2 macro for example: ===
1. Save the syntax into a .PY file. 2. Create new file named '''Color2.py''' and paste below script inside it under the following directories: - /usr/local/lib/python2.7/dist-packages/MoinMoin/macro - /usr/lib/python2.7/ 3. The file content is shown below:

{{{
"""
    MoinMoin - Color2 Macro

    @copyright: 2006 by Clif Kussmaul <clif@kussmaul.org>
                2008 by Clif Kussmaul, Dave Hein (MoinMoin:DaveHein)
                2011 by Clif Kussmaul, Dave Hein (MoinMoin:DaveHein), Gregor Mirai
    @license:   GNU GPL, see COPYING for details

    Usage: <<Color2(text,col=color,bcol=bgcolor,font=_font_)>>
           <<Color2(text,bcol=bgcolor)>>
           <<Color2(text,color)>>

    History:
    - 2011.02.23: [Moin 1.9] updated for Moin 1.9 by Gregor Mirai,
                  code simplified due to new parameter parsing and lots of other improvements in Moin code.
                  MiniPage functionality for the text parameter has been preserved.
    - 2008.01.25: [Moin 1.6] updated for Moin 1.6 by Dave Hein,
                  no functional changes.
    - 2006: [Moin 1.5] written by Clif Kussmaul
    - originally based on Color Macro
      Copyright (c) 2002 by Markus Gritsch <gritsch@iue.tuwien.ac.at>
"""

from MoinMoin import wikiutil
from MoinMoin.parser.text_moin_wiki import Parser as WikiParser

"""
    Parameters that macro accepts are:
        - text (the text to be colored)
        - col  (optional text color)
        - bcol (optional background text color)
        - font (optional font)

    Examples:

    <<Color2(Hello World!,col=red,bcol=blue,font=18px courier)>>
    <<Color2(Hello World!,bcol=blue)>>
    <<Color2(Hello World!,orange)>>
"""

def macro_Color2(macro, text=None, col=None, bcol=None, font=None):
    f = macro.formatter

    if not text:
        return f.strong(1) + \
               f.text('Color2 examples : ') + \
               f.text('<<Color2(Hello World!,red,blue,18px courier)>>, ') + \
               f.text('<<Color2(Hello World!,col=red,bcol=blue,font=18px courier)>>, ') + \
               f.text('<<Color2(Hello World!,#8844AA)>>') + \
               f.strong(0) + f.linebreak(0)

    style = ''
    if col:
        style += 'color:%s; '            % col
    if bcol:
        style += 'background-color:%s; ' % bcol
    if font:
        style += 'font:%s; '             % font

    # Escape HTML stuff.
    text = wikiutil.escape(text)
    text = wikiutil.renderText(macro.request, WikiParser, text)
    text = text.strip()

    return f.rawHTML('<span style="%s">' % style) + text + f.rawHTML('</span>')
}}}
== Activate Recent Changes Page ==
From the navigation bar choose the Recent Changes page and add the macro to it as following: <<BR>>

we use the << !MacroName >> to add macros to the page and << !RecentChanges >> Macro list all the recent changes happened on the wiki pages for more macros help all the pre-defined macros listed in the following link: <<BR>> http://moinmo.in/HelpOnMacros <<BR>>

== Moinmoin Themes ==
The default Theme of the '''Moinmoin''' 1.9.3 is Modern Theme however we still can find additional themes as shown in http://moinmo.in/ThemeMarket and download the archives there. Please make sure that the theme you choose is compatible with your version of '''Moinmoin'''! <<BR>> You cannot just copy the whole directory to a themes directory. That will not work. To install a new theme follow the instructions by the theme author. Generally you have two different locations: <<BR>> 1. The directory where all the static files of the themes reside <<BR>> 2. Unpack the archive and put the theme directory here: <<BR>> i.e. [/usr/local/share/moin/htdocs/fixedleft] (containing two directories: css/ and img/). <<BR>> The [/usr/local/share/moin/data/plugin/theme] directory where Theme code can reside move the theme script that has the same name as the theme but with a .py <<BR>>

=== Install the fixedleft Theme ===
i. Using the command

{{{
wget http://moinmo.in/ThemeMarket/FixedLeft?action=AttachFile&do=get&target=FixedLeft190.zip
}}}
2. Unzip the downloaded file. <<BR>> 3. Copy fixedleft.py to /usr/local/share/moin/data/plugin/theme/ <<BR>> 4. Use command line to create [/htdocs/fixedleft] folder as shown below <<BR>>

{{{
mkdir /usr/local/lib/python2.7/dist-packages/MoinMoin/web/static/htdocs/fixedleft
}}}
Copy css/ js/ img/ from extracted downloaded zip file to:

{{{
/usr/local/lib/python2.7/dist-packages/MoinMoin/web/static/htdocs/fixedleft
}}}
= Backup and Restore =
To back up the '''Moinmoin''' website we need to: <<BR>> 1- Prepare a backup server with a fresh installation of '''Moinmoin''' <<BR>> 2- Using the [ssh-keygen -t rsa] command to create a securely connection between the 2 servers so the Backup procedure won’t need a username and password <<BR>> 3- Define the folders that need to Backup <<BR>>

Using crontab command we will schedule the backup task

== Secure connection between the 2 servers ==
On the Source server of the '''Moinmoin''' we will run the Following Commands:

{{{
sudo ssh-keygen -t rsa
}}}
{{{
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
}}}
2 – Copy the Public key to the distention server

{{{
scp /root/.ssh/id_rsa.pub [destination IP: [Location]]
}}}
On the Destination server we will run the following command

{{{
sudo ssh-keygen
sudo cat [location]/id_rsa.pub >> .ssh/authorized_keys
}}}
== Define the Folders and files we need to backup ==
1. Moinmoin Folder i.e. [/usr/locale/share/moin] <<BR>> 2. web server configuration [/etc/apache2/apache2.conf ] <<BR>>

on the wiki server

{{{
rsync -r /usr/local/share/moin [Dest.IP]:/usr/local/share/
}}}
on the destination server

{{{
cd /usr/local/share
sudo chown -R www-data:www-data moin
sudo chmod -R ug+rwx moin
sudo chmod -R o-rwx moin
}}}
Save and Exit
