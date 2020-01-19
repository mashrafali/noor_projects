{{{
Author       : Ahmed AbdelSattaar
Version      : 1.1
Creation date: 2014-02-16
Latest update: 2014-02-16
}}}
<<TableOfContents(5)>>

= Export Distribution Lists : =
== Create Export script ==
- [[attachment:distribution-lists-export.sh]] script will generate '''distribution-lists-import.sh''' file which contian all distribution lists and members for every distribution list.

- It can use for Backup/Restore all distribution lists and members for every distribution list at same mail server.

or

- Export all distribution lists and members for every distribution list to another mail server.

- '''distribution-lists-import.sh''' file can be run as script and create exported distribution lists and members for every distribution list automaticlly.

== Edit script ==
{{{
    root@smtp:~#nano /opt/zimbra/backup/distribution-lists-export.sh
}}}
Then add the following

{{{#!/bin/bash


# customize these to your needs
# work folder must be writable by zimbra

backup_folder=/opt/zimbra/backup

date=`date --iso`

work_folder=${backup_folder}/zm-backup-${date}
dist_dump=${work_folder}/dist.dump
exclude_dist="system-virus-quarantine spam.fokhdgs_w ham.dg7nhzrqk virus-quarantine.0l8frvwb1d"

import_script=${work_folder}/distribution-lists-import.sh

# Create work_folder
mkdir -p ${work_folder}
chown -R zimbra: ${work_folder}

# reset files:
echo  '' > ${import_script}

# get all distributaion-lists to $dists

dists=`su - zimbra -c 'zmprov gadl | sort'`;

# loop for each distributaion-lists
for dist in ${dists}; do
  dist_uid=`echo ${dist} | cut -d '@' -f1`
  echo ${exclude_dist} | grep -q -e ${dist_uid}
  if [ $? -eq 0 ] ; then
    echo "Skip ${dist}"
    continue
  fi

  echo "Processing ${dist} ..."

    # generate import script
        echo "zmprov cdl ${dist}" >> ${import_script};

        su - zimbra -c "zmprov gdl ${dist} | grep zimbraMailForward | awk '{print \$2}'|sort > ${dist_dump}";
        for mem in $(cat  ${dist_dump});
        do
        echo "Adding member ${mem} to ${dist} List"
        echo "zmprov adlm ${dist} ${mem}" >> ${import_script};
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
    root@smtp:~#chmod a+x /opt/zimbra/backup/distribution-lists-export.sh
}}}
{{{
    root@smtp:~#/opt/zimbra/backup/distribution-lists-export.sh
}}}
Export script will create '''distribution-lists-import.sh '''under directory called '''zm-backup-[date]''' like zm-backup-2014-02-16

== Check script output ==
{{{
    root@smtp:~# ls -l /opt/zimbra/backup/zm-backup-2014-02-16/distribution-lists-import.sh

    root@smtp:~# less /opt/zimbra/backup/zm-backup-2014-02-16/distribution-lists-import.sh
}}}
= Import Distribution Lists: =
== Import script ==
'''distribution-lists-import.sh''' is generated after running '''distribution-lists-export.sh''' script and it contains zimbra commands and data that create  all distribution lists and members for every distribution list

its looks like

{{{
zmprov cdl it@noor.net
zmprov adlm it@noor.net aabdelsattaar@noor.net
zmprov adlm it@noor.net aali@noor.net
zmprov adlm it@noor.net abadr@noor.net
zmprov adlm it@noor.net atohamy@noor.net
zmprov adlm it@noor.net hhassan@noor.net
zmprov adlm it@noor.net kfarrag@noor.net
zmprov adlm it@noor.net rhesham@noor.net

.................
.................
.................
}}}
== Move Import script ==
Move''' ''''''distribution-lists-import.sh ''' file to destnation server incase of importing distribution lists and members for every distribution list at another mail server (IP: 192.168.0.85)

{{{
    root@smtp:~# cd /opt/zimbra/backup/zm-backup-2014-02-16/
    root@smtp:~# scp
 root@192.168.0.85:/opt/zimbra/backup/
}}}
== Run Import script ==
Move to backup directory then run '''distribution-lists-import.sh''' script as root

{{{
    root@smtp:~# cd /opt/zimbra/backup/
    root@smtp:~#./distribution-lists-import.sh
}}}
Then check that   all distribution lists and members for every distribution list have been imported and created
