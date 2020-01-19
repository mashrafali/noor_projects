{{{
Author       : Mohamed Ashraf
Title        : NOOR Domain Chekcer TOOL
Version      : 1.0
Creation date: 23/9/2014
Latest update: 23/9/2014
}}}



<<TableOfContents(4)>>



{{attachment:back.jpg}}




= Introduction =

Domain Checker is a tool that is used to check if a certain domain has services offered by NOOR or not.


'''''The Tool checks for the following Services'''''

1- If the Customer Domain is DNS Hosted by NOOR [Public or Shared Hosted].

2- If the Customer Domain is Mail Hosted by NOOR [Public or Shared Hosted].

3- If the Customer Domain is WEB Hosted by NOOR.

4- If the Customer is using NOOR's Queuing service. 



= Details =


- The whole idea of the tool is to host an HTML webpage which contains a form that is used to interface with the user and fetch the Customer domain name.

- The Fetched Data is then feeded to a CGI Script which is programmed to apply several tests on the domain Name.



= Requirements =


- The tool was built on Debian based Linux Dist. [Ubuntu 14.04 x64 was used to build the tool], however the tool can be hosted on any Linux based operating system.

- Apache web server is required to view and interact with the tool


= Source Code =

- Two Files were coded to interact with each other, one in HTML and the other is in bash

== HTML Code ==

- Below is the code for the HTML page hosted in the web server [/var/www/domain-checker/index.html]

{{{
<HTML>
<TITLE>Domain Checker</TITLE>
<style>
body  {
    background-image: url('light-color-blue-cyan-white-background.png');
   background-attachment: fixed;
background-size: 100% 100%;
}
</style>
<h1>NOOR Domain Checker</h1>
<FORM method="get" ACTION="http://dcheck.noor.net/cgi-bin/customer-Dcheck.cgi">
Enter Customer Domain Name: <INPUT TYPE="TEXT" NAME="Field"
SIZE="40"><BR>
<INPUT TYPE="SUBMIT" NAME="Submit" VALUE="Submit">
</FORM>
</BODY>
</HTML> 
}}}

== CGI Script ==

- Below is the code for the CGI SCRIPT hosted in the CGI Directory at the web server or any other compatible server [/usr/lib/cgi-bin/customer-Dcheck.cgi]

{{{
#!/bin/bash


POST=$QUERY_STRING
DIN=$(echo $POST | cut -d '=' -f 2 | cut -d '&' -f 1)

# Fetching DATA

echo "Content-type: text/html"
echo ""
echo "<html><head><title>Domain Checker"
echo '</title></head>'
echo "<style>
body  {
   background-image: url('http://www.kombi-ker.hu/wp-content/uploads/2014/11/Black-And-White-Abstract-Background-Pictures-5-HD-Wallpapers.jpg');
   background-attachment: fixed;
background-size: 100% 100%;
}
</style>"
echo ""
echo "<h1> NOOR Domain Checker </h1>"
echo "<h2>Domain: $DIN </h2>"
echo "<h3>------------------------------------------------------------------------------------------</h3>"
################################################################################## Running TEST script
############################################
# CUSTOMER DOMAIN CHECKER BY MASHRAF
############################################

# Reading data

CDOMAIN=$DIN

if [ -z "$CDOMAIN" ]                    # Terminate if no input
  then
   echo; echo "Please Provide a customer Domain Name!"
   echo
   kill $$
fi

if [ "$CDOMAIN" == "noor.net" ]                    # Terminate if its a joke!
  then
   echo; echo "The Tool will not waste it's resources for this, This tool is to test CUSTOMERS Domain Names."
   echo
   kill $$
fi

if [ "$CDOMAIN" == "noorgroup.net" ]                    # Terminate if its a joke!
  then
   echo; echo "The Tool will not waste it's resources for this, This tool is to test CUSTOMERS Domain Names."
   echo
   kill $$
fi


# Clearing new report

echo > report
echo "<pre> ####################################################################################### </pre>"  >> report
echo "<pre> Full Tests Report: </pre>"                        >> report
echo "<pre> ####################################################################################### </pre>"  >> report
# Starting tests:

echo
echo "<h3> Checking Status for Domain: $CDOMAIN </h3>"
echo "<h3> -- </h3>"
echo


################################################## TEST 1 [DNS]:
echo
echo -n " <h3> Test 1 : Customer is DNS Hosted at NOOR  :"
nslookup -q=ns $CDOMAIN 8.8.8.8 > verify.tmp

A1=$(cat verify.tmp | grep -i "nameserver = " |grep -i dns0.noor.net | wc -l)
A2=$(cat verify.tmp | grep -i "nameserver = " |grep -i dns1.noor.net | wc -l)
A3=$(cat verify.tmp | grep -i "nameserver = " |grep -i ns1.noor.com | wc -l)
A4=$(cat verify.tmp | grep -i "nameserver = " |grep -i ns2.noor.com | wc -l)


if [ "$A1" -ge "1" ]
  then
   check1=ok
   echo "<pre> Customer IS DNS Hosted @NOOR   : Found at dns0.noor.net </pre>"  >> report
   result1="Public Hosted"
fi

if [ "$A1" -ge "1" ]
  then
   check2=ok
   echo "<pre> Customer IS DNS Hosted @NOOR   : Found at dns1.noor.net </pre>"  >> report
   result1="Public Hosted"
fi

if [ "$A3" -ge "1" ]
  then
   check3=ok
   echo "<pre> Customer IS DNS Hosted @NOOR   : Found at ns1.noor.com </pre>"  >> report
   result1="Shared Hosted"
fi

if [ "$A3" -ge "1" ]
  then
   check4=ok
   echo "<pre> Customer IS DNS Hosted @NOOR   : Found at ns2.noor.com </pre>"  >> report 
   result1="Shared Hosted"
fi



if [ -n "$check1" ] || [ -n "$check2" ] || [ -n "$check3" ] || [ -n "$check4" ]
  then
   echo "    YES ($result1) </h3>"
  else
   echo "    NO </h3>"
   echo "<pre> NO DNS Hosting Record Found For This Domain </pre>"  >> report
fi

################################################### TEST 2 [MAIL]
echo
echo -n " <h3> Test 2 : Customer is Mail Hosted at NOOR :"
nslookup -q=mx $CDOMAIN 8.8.8.8 > verify.tmp

B1=$(cat verify.tmp | grep -i "mail exchanger = " | grep -i mail.noor.com | wc -l)
B2=$(cat verify.tmp | grep -i "mail exchanger = " | grep -i mail3.noor.com | wc -l)
if [ "$B1" -ge "1" ]
  then
   check5=ok
   echo "<pre> Customer IS MAIL Hosted @NOOR  : Found at mail.noor.com </pre>"  >> report
   result2="Shared Hosted"
fi

if [ "$B2" -ge "1" ]
  then
   check6=ok
   echo "<pre> Customer IS MAIL Hosted @NOOR  : Found at mail3.noor.com </pre>"  >> report
   result2="Shared Hosted"
fi

if [ -n "$check5" ] || [ -n "$check6" ]
  then
   echo "    YES ($result2) </h3>"
  else
   nslookup -q=mx $CDOMAIN 8.8.8.8 | grep -i "mail exchanger =" | cut -d" " -f5 > ListQ.tmp
   while read listQ
    do
        if [ "$listQ" == "mail.noor.net." ]
          then
             continue
        fi
        nslookup -q=a $listQ 8.8.8.8 > verify.tmp
        B3=$(cat verify.tmp | grep -i "41.187.100."  | wc -l)
        B4=$(cat verify.tmp | grep -i "41.187.101."  | wc -l)
        B5=$(cat verify.tmp | grep -i "217.139.226." | wc -l)
        B6=$(cat verify.tmp | grep -i "217.139.227." | wc -l)

       if [ "$B3" -ge "1" ]
         then
          check7=ok
          echo "<pre> Customer IS MAIL Hosted @NOOR  : Found at range 41.187.100.x </pre>"  >> report
          result2="NOOR Hosted"
       fi
       if [ "$B4" -ge "1" ]
         then
          check8=ok
          echo "<pre> Customer IS MAIL Hosted @NOOR  : Found at range 41.187.101.x </pre>"  >> report
          result2="NOOR Hosted"
       fi
       if [ "$B5" -ge "1" ]
         then
          check9=ok
          echo "<pre> Customer IS MAIL Hosted @NOOR  : Found at range 217.139.226.x </pre>"  >> report
          result2="NOOR Hosted"
       fi
       if [ "$B6" -ge "1" ]
         then
          check10=ok
          echo "<pre> Customer IS MAIL Hosted @NOOR  : Found at range 217.139.227.x </pre>"  >> report
          result2="NOOR Hosted"
       fi
       if [ -n "$check7" ] || [ -n "$check8" ] || [ -n "$check9" ] || [ -n "$check10" ]
       then
         echo "    YES ($result2) </h3>"
       else
         Tcheck="fail"
         #echo "    NO </h3>"
         #echo "<pre> NO MAIL Hosting Record Found For This Domain </pre>"  >> report
       fi
    done < ListQ.tmp
    if [ "$Tcheck" == "fail" ]
     then
      echo "    NO </h3>"
      echo "<pre> NO MAIL Hosting Record Found For This Domain </pre>"  >> report
    fi
fi

################################################### TEST 3 [WEB]
echo
echo -n "<h3> Test 3 : Customer is WEB Hosted at NOOR  :"
nslookup -q=a www.$CDOMAIN 8.8.8.8 > verify.tmp

C1=$(cat verify.tmp | grep -i "41.187.100."  | wc -l)
C2=$(cat verify.tmp | grep -i "41.187.101."  | wc -l)
C3=$(cat verify.tmp | grep -i "217.139.226." | wc -l)
C4=$(cat verify.tmp | grep -i "217.139.227." | wc -l)


if [ "$C1" -ge "1" ]
  then
   check11=ok
   echo "<pre> Customer IS WEB Hosted @NOOR   : Found at range 41.187.100.x </pre>"  >> report
   result3="Range 100"
fi
if [ "$C2" -ge "1" ]
  then
   check12=ok
   echo "<pre> Customer IS WEB Hosted @NOOR   : Found at range 41.187.101.x </pre>"  >> report
   result3="Range 101"
fi
if [ "$C3" -ge "1" ]
  then
   check13=ok
   echo "<pre> Customer IS WEB Hosted @NOOR   : Found at range 217.139.226.x </pre>"  >> report
   result3="Range 226"
fi
if [ "$C4" -ge "1" ]
  then
   check14=ok
   echo "<pre> Customer IS WEB Hosted @NOOR   : Found at range 217.139.227.x </pre>"  >> report
   result3="Range 227"
fi
if [ -n "$check11" ] || [ -n "$check12" ] || [ -n "$check13" ] || [ -n "$check14" ]
then
  echo "    YES ($result3) </h3>"
else
  echo "    NO </h3>"
  echo "<pre> NO WEB Hosting Record Found For This Domain </pre>"  >> report  
fi

################################################### TEST 4 [Queing]
echo
echo -n " <h3> Test 4 : Is Using NOOR's Queuing Service :"
nslookup -q=mx $CDOMAIN 8.8.8.8 | grep -i "mail exchanger =" | cut -d" " -f5 > verify.tmp
count=$(cat verify.tmp | wc -l)

if [ "$count" -ge "2" ]
   then
     D1=$(cat verify.tmp | grep -i "mail.noor.net"  | wc -l)
     if [ "$D1" -ge "1" ]
       then
        check15=ok
        echo "<pre> Customer Using Queuing Service : mail.noor.net found within preference alongside others </pre>"  >> report
        result4="Service is preferenced"
        echo "    YES ($result4) </h3>"
       else
        echo "    NO </h3>"
        echo "<pre> NO Record For Queuing Service Found For This Domain </pre>"  >> report 
     fi
    else
     echo "    NO </h3>"
     echo "<pre> NO Record For Queuing Service Found For This Domain </pre>"  >> report 
fi

################################################################## End of Tests
echo
echo


################################ Reporting results
echo "<h3> -- </h3>"

#echo " <pre> TESTING FORMAT FONT </pre>"

cat report

echo
echo


# The End...
}}}




= Virtual Host  (By Ahmed AbdelSattar) =

- In order to access the tool using it's assigned name on a web server that has other web services, a Virtual Host is created as below.


== APACHE 2.2 Server Configuration ==

- Append the following to the Apache sites configuration file located @[/etc/apache2/sites-available/default]

{{{
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName dcheck.noor.net                                             <-- Site name
        ServerAlias www.dcheck.noor.net                                        <-- Site alias, if u need to add other names to the same site
 
        DocumentRoot /var/www/domain-checker                                   <-- Site Root Directoy
        <Directory />
                Options FollowSymLinks
                AllowOverride None
        </Directory>
        <Directory /var/www/>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                allow from all
        </Directory>

        ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/                                 <-- CGI Directory [leave as default if u have added your script @/usr/lib/cgi-bin]
        <Directory "/usr/lib/cgi-bin">
                AllowOverride None
                Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                Order allow,deny
                Allow from all
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/access.log combined

    Alias /doc/ "/usr/share/doc/"
    <Directory "/usr/share/doc/">
        Options Indexes MultiViews FollowSymLinks
        AllowOverride None
        Order deny,allow
        Deny from all
        Allow from 127.0.0.0/255.0.0.0 ::1/128
    </Directory>

</VirtualHost>
}}}

== APACHE 2.4 Server Configuration ==

- Append the following to the Apache sites configuration file located @[/etc/apache2/sites-available/default]

{{{
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName dcheck.noor.net
        ServerAlias www.dcheck.noor.net

        DocumentRoot /var/www/domain-checker
        <Directory />
                Options +FollowSymLinks
                AllowOverride None
        </Directory>
        <Directory /var/www/>
                Options +Indexes +FollowSymLinks +MultiViews
                AllowOverride None
                Require all granted
        </Directory>

        ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
        <Directory "/usr/lib/cgi-bin">
                AllowOverride None
                Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                Require all granted
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/access.log combined

    Alias /doc/ "/usr/share/doc/"
    <Directory "/usr/share/doc/">
        Options +Indexes +MultiViews +FollowSymLinks
        AllowOverride None
        Require all granted
        Allow from 127.0.0.0/255.0.0.0 ::1/128
    </Directory>

</VirtualHost>
}}}
== Startup Virtual Host ==

 Execute the Below as root:

{{{
a2ensite dcheck.noor.net                    <-- V Host Name
}}}

 Then Finally Restart Apache Server to Apply Changes

{{{
service apache2 restart
}}}



= Tool's Address =

NOOR's Domain Checker Address: 

{{{
http://dcheck.noor.net/          [Hosted @192.168.0.114 (Apt-Cacher)]
}}}
