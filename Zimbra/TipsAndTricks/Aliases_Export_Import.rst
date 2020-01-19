{{{
Author       : Ahmed AbdelSattaar
Version      : 1.1
Creation date: 2014-02-16
Latest update: 2014-02-16
}}}
<<TableOfContents(5)>>

= Export Alias and Forwarding Addresses: =
== Create Export script ==
- [[attachment:accounts-fw-alias-export.sh]] script will generate '''accounts-fw-alias-import.sh ''' file which contian accounts Alias and forwarding addresses data for '''Zimbra''' mail server.

- It can use for Backup/Restore accounts Alias and forwarding addresses at same mail server.

or

- Export accounts Alias and forwarding addresses and import it at another mail server.

- '''accounts-fw-alias-import.sh''' file can be run as script and create exported alias and forwarding addresses automaticlly.

== Edit script ==
{{{
    root@smtp:~#nano /opt/zimbra/backup/accounts-fw-alias-export.sh
}}}
Then add the following

{{{

#!/bin/bash

# customize these to your needs
# work folder must be writable by zimbra

backup_folder=/opt/zimbra/backup

date=`date --iso`

work_folder=${backup_folder}/zm-backup-${date}
account_dump=${work_folder}/account.dump
exclude_accounts="admin wiki galsync system-ham system-spam system-virus-quarantine spam.fokhdgs_w ham.dg7nhzrqk virus-quarantine.0l8frvwb1d"

import_script=${work_folder}/accounts-fw-alias-import.sh

# Create work_folder
mkdir -p ${work_folder}
chown -R zimbra: ${work_folder}

# reset files:
echo  '' > ${import_script}

# get all account to $accounts
accounts=`su - zimbra -c 'zmprov -l gaa | sort'`;

# loop for each account
for account in ${accounts}; do
  account_uid=`echo ${account} | cut -d '@' -f1`  
  echo ${exclude_accounts} | grep -q -e ${account_uid}
  if [ $? -eq 0 ] ; then
    echo "Skip ${account}"
    continue
  fi

  echo "Processing ${account} ..."
  su - zimbra -c "zmprov -l ga ${account} uid givenName sn cn displayName mail zimbraMailForwardingAddress > ${account_dump}"
  
  # generate import script

  # get all aliases
  for newalias in `grep -e ^mail: ${account_dump} | cut -d ':' -f2 | sed 's/^ *//g' | sed 's/ *$//g'`; do
    if [ "x${newalias}" != "x${account}" ]; then
       echo "Extracting alias ${newalias} for ${account} ..."
       echo "zmprov aaa ${account} ${newalias}" >> ${import_script}
    fi
  done


  # get all  forwarding address
  for newforward in `grep -e ^zimbraMailForwardingAddress: ${account_dump} | cut -d ':' -f2 | sed 's/^ *//g' | sed 's/ *$//g'`; do
    if [ "x${newforward}" != "x${account}" ]; then
       echo "Extracting Forwarding address  ${newforward} for ${account} ..."
       echo "zmprov ma ${account} +zimbraMailForwardingAddress ${newforward}" >> ${import_script}

    fi
  done

  
  # add blank line separator
  echo '' >>  ${import_script}
    
done

}}}
== Run Export script ==
As root run the following commands

{{{
    root@smtp:~#cd /opt/zimbra/backup/
}}}
{{{
    root@smtp:~#chmod a+x /opt/zimbra/backup/accounts-fw-alias-export.sh
}}}
{{{
    root@smtp:~#/opt/zimbra/backup/accounts-fw-alias-export.sh
}}}
Export script will create '''accounts-fw-alias-import.sh '''under directory called '''zm-backup-[date]''' like zm-backup-2014-02-16

== Check script output ==
{{{
    root@smtp:~# ls -l /opt/zimbra/backup/zm-backup-2014-02-16/accounts-fw-alias-import.sh

    root@smtp:~# less /opt/zimbra/backup/zm-backup-2014-02-16/accounts-fw-alias-import.sh
}}}

= Import Alias and Forwarding Addresses: =
== Import script ==
'''accounts-fw-alias-import.sh''' is generated after running '''accounts-fw-alias-export.sh''' script and it contains zimbra commands and data that create accounts alias and forwarding addresses

its looks like

{{{
zmprov aaa aabdelsattaar@noor.net ahmed.abdelsattaar@noor.net

zmprov aaa atohamy@noor.net ayman.tohamy@noor.net

zmprov ma contacts@noor.net +zimbraMailForwardingAddress contacts-group@noor.net

zmprov ma cs@nooradsl.com +zimbraMailForwardingAddress customer.service@nooradsl.com

.................
.................
.................
}}}
== Move Import script ==
Move '''accounts-fw-alias-import.sh''' file to destnation server incase of importing accounts alias and Forward addresses at another mail server (IP: 192.168.0.85)

{{{
    root@smtp:~# cd /opt/zimbra/backup/zm-backup-2014-02-16/
    root@smtp:~# scp accounts-fw-alias-import.sh root@192.168.0.85:/opt/zimbra/backup/
}}}
== Run Import script ==
Move to backup directory then run '''accounts-fw-alias-import.sh''' script as root

{{{
    root@smtp:~# cd /opt/zimbra/backup/
    root@smtp:~#./accounts-fw-alias-import.sh
}}}

Then check that accounts alias and Forward addresses has been imported 
