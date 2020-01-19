{{{
Author       : Mohamed Ashraf
Title        : Active Synchronization Module
Version      : 1.0
Creation date: 15/1/2015
Latest update: 15/1/2015
}}}

<<TableOfContents(4)>>


{{attachment:logo.png}}



= Introduction =

The tool that is going to be used for this module is "rsync".

Rsync, which stands for "remote sync", is a remote and local file synchronization tool. It uses an algorithm that minimizes the amount of data copied by only moving the portions of files that have changed.

Due to its wide spread on Linux and Unix-like systems and its popularity as a tool for system scripts, it is included on most Linux distributions by default.

= Options =

The following options/flags can be used with rsync

{{{
 -v, --verbose               increase verbosity
     --info=FLAGS            fine-grained informational verbosity
     --debug=FLAGS           fine-grained debug verbosity
     --msgs2stderr           special output handling for debugging
 -q, --quiet                 suppress non-error messages
     --no-motd               suppress daemon-mode MOTD (see manpage caveat)
 -c, --checksum              skip based on checksum, not mod-time & size
 -a, --archive               archive mode; equals -rlptgoD (no -H,-A,-X)
     --no-OPTION             turn off an implied OPTION (e.g. --no-D)
 -r, --recursive             recurse into directories
 -R, --relative              use relative path names
     --no-implied-dirs       don't send implied dirs with --relative
 -b, --backup                make backups (see --suffix & --backup-dir)
     --backup-dir=DIR        make backups into hierarchy based in DIR
     --suffix=SUFFIX         set backup suffix (default ~ w/o --backup-dir)
 -u, --update                skip files that are newer on the receiver
     --inplace               update destination files in-place (SEE MAN PAGE)
     --append                append data onto shorter files
     --append-verify         like --append, but with old data in file checksum
 -d, --dirs                  transfer directories without recursing
 -l, --links                 copy symlinks as symlinks
 -L, --copy-links            transform symlink into referent file/dir
     --copy-unsafe-links     only "unsafe" symlinks are transformed
     --safe-links            ignore symlinks that point outside the source tree
     --munge-links           munge symlinks to make them safer (but unusable)
 -k, --copy-dirlinks         transform symlink to a dir into referent dir
 -K, --keep-dirlinks         treat symlinked dir on receiver as dir
 -H, --hard-links            preserve hard links
 -p, --perms                 preserve permissions
 -E, --executability         preserve the file's executability
     --chmod=CHMOD           affect file and/or directory permissions
 -A, --acls                  preserve ACLs (implies --perms)
 -X, --xattrs                preserve extended attributes
 -o, --owner                 preserve owner (super-user only)
 -g, --group                 preserve group
     --devices               preserve device files (super-user only)
     --specials              preserve special files
 -D                          same as --devices --specials
 -t, --times                 preserve modification times
 -O, --omit-dir-times        omit directories from --times
 -J, --omit-link-times       omit symlinks from --times
     --super                 receiver attempts super-user activities
     --fake-super            store/recover privileged attrs using xattrs
 -S, --sparse                handle sparse files efficiently
     --preallocate           allocate dest files before writing them
 -n, --dry-run               perform a trial run with no changes made
 -W, --whole-file            copy files whole (without delta-xfer algorithm)
 -x, --one-file-system       don't cross filesystem boundaries
 -B, --block-size=SIZE       force a fixed checksum block-size
 -e, --rsh=COMMAND           specify the remote shell to use
     --rsync-path=PROGRAM    specify the rsync to run on the remote machine
     --existing              skip creating new files on receiver
     --ignore-existing       skip updating files that already exist on receiver
     --remove-source-files   sender removes synchronized files (non-dirs)
     --del                   an alias for --delete-during
     --delete                delete extraneous files from destination dirs
     --delete-before         receiver deletes before transfer, not during
     --delete-during         receiver deletes during the transfer
     --delete-delay          find deletions during, delete after
     --delete-after          receiver deletes after transfer, not during
     --delete-excluded       also delete excluded files from destination dirs
     --ignore-missing-args   ignore missing source args without error
     --delete-missing-args   delete missing source args from destination
     --ignore-errors         delete even if there are I/O errors
     --force                 force deletion of directories even if not empty
     --max-delete=NUM        don't delete more than NUM files
     --max-size=SIZE         don't transfer any file larger than SIZE
     --min-size=SIZE         don't transfer any file smaller than SIZE
     --partial               keep partially transferred files
     --partial-dir=DIR       put a partially transferred file into DIR
     --delay-updates         put all updated files into place at transfer's end
 -m, --prune-empty-dirs      prune empty directory chains from the file-list
     --numeric-ids           don't map uid/gid values by user/group name
     --usermap=STRING        custom username mapping
     --groupmap=STRING       custom groupname mapping
     --chown=USER:GROUP      simple username/groupname mapping
     --timeout=SECONDS       set I/O timeout in seconds
     --contimeout=SECONDS    set daemon connection timeout in seconds
 -I, --ignore-times          don't skip files that match in size and mod-time
 -M, --remote-option=OPTION  send OPTION to the remote side only
     --size-only             skip files that match in size
     --modify-window=NUM     compare mod-times with reduced accuracy
 -T, --temp-dir=DIR          create temporary files in directory DIR
 -y, --fuzzy                 find similar file for basis if no dest file
     --compare-dest=DIR      also compare destination files relative to DIR
     --copy-dest=DIR         ... and include copies of unchanged files
     --link-dest=DIR         hardlink to files in DIR when unchanged
 -z, --compress              compress file data during the transfer
     --compress-level=NUM    explicitly set compression level
     --skip-compress=LIST    skip compressing files with a suffix in LIST
 -C, --cvs-exclude           auto-ignore files the same way CVS does
 -f, --filter=RULE           add a file-filtering RULE
 -F                          same as --filter='dir-merge /.rsync-filter'
                             repeated: --filter='- .rsync-filter'
     --exclude=PATTERN       exclude files matching PATTERN
     --exclude-from=FILE     read exclude patterns from FILE
     --include=PATTERN       don't exclude files matching PATTERN
     --include-from=FILE     read include patterns from FILE
     --files-from=FILE       read list of source-file names from FILE
 -0, --from0                 all *-from/filter files are delimited by 0s
 -s, --protect-args          no space-splitting; only wildcard special-chars
     --address=ADDRESS       bind address for outgoing socket to daemon
     --port=PORT             specify double-colon alternate port number
     --sockopts=OPTIONS      specify custom TCP options
     --blocking-io           use blocking I/O for the remote shell
     --stats                 give some file-transfer stats
 -8, --8-bit-output          leave high-bit chars unescaped in output
 -h, --human-readable        output numbers in a human-readable format
     --progress              show progress during transfer
 -P                          same as --partial --progress
 -i, --itemize-changes       output a change-summary for all updates
     --out-format=FORMAT     output updates using the specified FORMAT
     --log-file=FILE         log what we're doing to the specified FILE
     --log-file-format=FMT   log updates using the specified FMT
     --password-file=FILE    read daemon-access password from FILE
     --list-only             list the files instead of copying them
     --bwlimit=RATE          limit socket I/O bandwidth
     --outbuf=N|L|B          set output buffering to None, Line, or Block
     --write-batch=FILE      write a batched update to FILE
     --only-write-batch=FILE like --write-batch but w/o updating destination
     --read-batch=FILE       read a batched update from FILE
     --protocol=NUM          force an older protocol version to be used
     --iconv=CONVERT_SPEC    request charset conversion of filenames
     --checksum-seed=NUM     set block/file checksum seed (advanced)
 -4, --ipv4                  prefer IPv4
 -6, --ipv6                  prefer IPv6
     --version               print version number
(-h) --help                  show this help (-h is --help only if used alone)
}}}

= Usage =

At Sync request, the tool will check for changes and only apply those changes during the operation.

It does not copy over the whole content because simply it detected an added line to a file, but it only syncs the '''changes'''.

== Push Operation ==

To feed data to remote server.

{{{
rsync -azP /root/CLUSTER-CONFIGURATION/var-www/ root@node2:/var/www/
}}}

== Pull Operation ==

To Fetch data from remote server.

{{{
rsync -azP root@node1:/etc/postfix/ /root/CLUSTER-CONFIGURATION/etc-postfix/
}}}

== Exclude Operation ==

To exclude a file during the fetch, can be done during fetch or feed operations.

{{{
rsync -azP root@node1:/var/www/ /root/CLUSTER-CONFIGURATION/var-www/ --exclude=connection.cfg --exclude=SYN-LINK
}}}

= Operation Examples =

The following are examples that has been used in operation for Active Sync.

The below Scripts can then be added to crontab for Actively syncing every desired cycle.

== SMTP Filter Cluster Sync ==

{{{
 /bin/bash       <--- Parser



## SYNCING CLUSTER CONFIGS
#Script by Mohamed Ashraf


# Locking SELF to Prevent Simultanious Execution
lockfile-create /root/CLUSTER-SYNC/cluster-config-sync.sh

echo
echo
echo "                                       SYNCING CONFIGURATION FILES OVER THE CLUSTER"
echo
echo "                                                                                                     By Mohamed Ashraf"
echo "################################################### PHASE 1 ############ Fetching From Main Node #####################"
echo
sleep 2
echo "-> Fetching configs from Master Node [10.10.10.1]"
echo "-------------------------------------------------"
echo
sleep 2
rsync -azP root@node1:/var/www/ /root/CLUSTER-CONFIGURATION/var-www/ --exclude=connection.cfg --exclude=SYN-LINK
rsync -azP root@node1:/etc/postfix/ /root/CLUSTER-CONFIGURATION/etc-postfix/
rsync -azP root@node1:/etc/spamassassin/ /root/CLUSTER-CONFIGURATION/etc-spamassassin/
# DB Lock Check
if ssh root@node1 "[ -f /var/lib/amavis/.spamassassin/*.lock ]"
then
    echo " ####################################################################### LOCK FILE FOUND FOR Bayesian DataBase!"
    sleep 2
    echo " ####################################################################### !! DB Sync will not continue !!"
    echo
else
    echo " ####################################################################### No Lock File found for DB, Continuing Sync.."
    sleep 1
    echo
    ssh root@node1 'lockfile-create /var/lib/amavis/.spamassassin/bayes_seen'                  # Creating Lock
    ssh root@node1 'lockfile-create /var/lib/amavis/.spamassassin/bayes_toks'                  # Creating Lock
    rsync -azP root@node1:/var/lib/amavis/.spamassassin/ /root/CLUSTER-CONFIGURATION/var-lib-amavis-.spamassassin/ --exclude=*.lock    #Syncing bias DB excpet Lock files
    ssh root@node1 'chown amavis:amavis /var/lib/amavis/.spamassassin/*'                       # Making sure the owner is right
    ssh root@node1 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_seen'                  # RemoVing Lock
    ssh root@node1 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_toks'                  # Removing Lock
fi
echo
echo
echo "################################################### PHASE 2 ############ Feeding The Slave Nodes #####################"
echo
sleep 2
echo "-> Publishing Configuration Over the Cluster Nodes"
echo "--------------------------------------------------"
echo
sleep 2
echo
echo "## Feeding node2 [10.10.10.2]"
echo "-----------------------------"
echo
echo
sleep 2
rsync -azP /root/CLUSTER-CONFIGURATION/var-www/ root@node2:/var/www/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-postfix/ root@node2:/etc/postfix/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-spamassassin/ root@node2:/etc/spamassassin/
# DB Lock Check
if ssh root@node2 "[ -f /var/lib/amavis/.spamassassin/*.lock ]"
then
    echo " ####################################################################### LOCK FILE FOUND FOR Bayesian DataBase!"
    sleep 2
    echo " ####################################################################### !! DB Sync will not continue !!"
    echo
else
    echo " ####################################################################### No Lock File found for DB, Continuing Sync.."
    sleep 1
    echo
    ssh root@node2 'lockfile-create /var/lib/amavis/.spamassassin/bayes_seen'                  # Creating Lock
    ssh root@node2 'lockfile-create /var/lib/amavis/.spamassassin/bayes_toks'                  # Creating Lock
    rsync -azP /root/CLUSTER-CONFIGURATION/var-lib-amavis-.spamassassin/ root@node2:/var/lib/amavis/.spamassassin/      #Syncing bias DB
    ssh root@node2 'chown amavis:amavis /var/lib/amavis/.spamassassin/*'                       # Making sure the owner is right
    ssh root@node2 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_seen'                  # RemoVing Lock
    ssh root@node2 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_toks'                  # Removing Lock
fi
echo
echo
echo "## Feeding node3 [10.10.10.3]"
echo "-----------------------------"
echo
echo
sleep 2
rsync -azP /root/CLUSTER-CONFIGURATION/var-www/ root@node3:/var/www/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-postfix/ root@node3:/etc/postfix/
rsync -azP /root/CLUSTER-CONFIGURATION/etc-spamassassin/ root@node3:/etc/spamassassin/
# DB Lock Check
if ssh root@node3 "[ -f /var/lib/amavis/.spamassassin/*.lock ]"
then
    echo " ####################################################################### LOCK FILE FOUND FOR Bayesian DataBase!"
    sleep 2
    echo " ####################################################################### !! DB Sync will not continue !!"
    echo
else
    echo " ####################################################################### No Lock File found for DB, Continuing Sync.."
    sleep 1
    echo
    ssh root@node3 'lockfile-create /var/lib/amavis/.spamassassin/bayes_seen'                  # Creating Lock
    ssh root@node3 'lockfile-create /var/lib/amavis/.spamassassin/bayes_toks'                  # Creating Lock
    rsync -azP /root/CLUSTER-CONFIGURATION/var-lib-amavis-.spamassassin/ root@node3:/var/lib/amavis/.spamassassin/      #Syncing bias DB
    ssh root@node3 'chown amavis:amavis /var/lib/amavis/.spamassassin/*'                       # Making sure the owner is right
    ssh root@node3 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_seen'                  # RemoVing Lock
    ssh root@node3 'lockfile-remove /var/lib/amavis/.spamassassin/bayes_toks'                  # Removing Lock
fi
echo
echo
echo "################################################### PHASE 3 ############ Restarting Nodes Services ###################"
echo
echo
sleep 2
ssh root@node2 "reboot" > /dev/null
echo
for i in {1..5}
do
echo -en " Slave-Node-2 Realizing New Configuration   -" \\r
sleep 0.25
echo -en " Slave-Node-2 Realizing New Configuration   /" \\r
sleep 0.25
echo -en " Slave-Node-2 Realizing New Configuration   |" \\r
sleep 0.25
echo -en ' Slave-Node-2 Realizing New Configuration   \' \\r
sleep 0.25
done
echo -en ' Slave-Node-2 Realizing New Configuration   OK!' \\r
echo
echo
ssh root@node3 "reboot" > /dev/null
echo
for i in {1..5}
do
echo -en " Slave-Node-3 Realizing New Configuration   -" \\r             
sleep 0.25
echo -en " Slave-Node-3 Realizing New Configuration   /" \\r
sleep 0.25
echo -en " Slave-Node-3 Realizing New Configuration   |" \\r
sleep 0.25
echo -en ' Slave-Node-3 Realizing New Configuration   \' \\r
sleep 0.25
done
echo -en ' Slave-Node-2 Realizing New Configuration   OK!' \\r
echo
echo
echo "                       -----------------> Cluster Syncronization Complete <-----------------       "
echo
echo

# BREAKING SELF LOCK
lockfile-remove /root/CLUSTER-SYNC/cluster-config-sync.sh
}}}


== Kerberos Active Sync ==

{{{
 /bin/bash       <--- Parser

## Defining MASTER IP
Sensai=10.9.9.222

## ENV INIT
cd /root/master-sync
########################################## WHO AM I CHECK
ping -c 1 -w 3 $Sensai

if [ "$?" = 0 ]
then
  echo " ##########################################  MY SENSAI IS ALIVE... LEARNING STUFF !"
  rank=$(cat PROMOTE)
  if [ "$rank" = 0 ]
  then
      echo " ############# I AM ONLY A NOOB!"
      ## SYNCING TACACS
      rsync -razP root@$Sensai:/etc/tacacs+/ /etc/tacacs+/
      ## SYNCING RANCID
      rsync -razP root@$Sensai:/var/lib/rancid/ /var/lib/rancid/
      rsync -razP root@$Sensai:/usr/lib/rancid/ /usr/lib/rancid/
      rsync -razP root@$Sensai:/etc/rancid/ /etc/rancid/
      ## SYNCING CVSWEB
      rsync -razP root@$Sensai:/etc/cvsweb/ /etc/cvsweb/
      ## SYNCING APACHE TO CVS INJECTION Link(s) : For any future flexibilty
      rsync -razP root@$Sensai:/var/www/ /var/www/
      ## SYNCING MAIL SERVER CONFIGURATIONS
      rsync -razP root@$Sensai:/etc/postfix/ /etc/postfix/
      rsync -razP root@$Sensai:/etc/aliases /etc/aliases
      ## SYNCING Logs
      rsync -azP root@$Sensai:/var/log/ /var/log/
      rsync -azP root@$Sensai:/etc/logrotate.d/ /etc/logrotate.d/
      ## SYNCING SYSTEM-CRON
      rsync -razP root@$Sensai:/etc/crontab /etc/crontab
      ## SYNCING ALIASES
      rsync -razP root@$Sensai:/etc/bash.bashrc /etc/bash.bashrc
      ## SYNCING USERS
      rsync -azP root@$Sensai:/root/ /root/ --exclude=.ssh/  --exclude=master-sync/
      rsync -azP root@$Sensai:/home/ /home/ --exclude=.ssh/  --exclude=master-sync/
      rsync -azP root@$Sensai:/etc/shadow /etc/shadow
      rsync -azP root@$Sensai:/etc/shadow- /etc/shadow-
      rsync -azP root@$Sensai:/etc/passwd /etc/passwd
      rsync -azP root@$Sensai:/etc/passwd- /etc/passwd-
      rsync -azP root@$Sensai:/etc/group /etc/group
      rsync -azP root@$Sensai:/etc/group- /etc/group-
      rsync -azP root@$Sensai:/etc/gshadow /etc/gshadow
      rsync -azP root@$Sensai:/etc/gshadow- /etc/gshadow-
  else
      echo " ############# I AM A MASTER TEACHING PRO!"
      ## REVERSE TACACS
      rsync -razP /etc/tacacs+/ root@$Sensai:/etc/tacacs+/
      ## REVERSE RANCID
      rsync -razP /var/lib/rancid/ root@$Sensai:/var/lib/rancid/
      rsync -razP /usr/lib/rancid/ root@$Sensai:/usr/lib/rancid/
      rsync -razP /etc/rancid/ root@$Sensai:/etc/rancid/
      ## REVERSE CVSWEB
      rsync -razP /etc/cvsweb/ root@$Sensai:/etc/cvsweb/
      ## REVERSE APACHE TO CVS INJECTION Link(s) : For any future flexibilty
      rsync -razP /var/www/ root@$Sensai:/var/www/
      ## REVERSE MAIL SERVER CONFIGURATIONS
      rsync -razP /etc/postfix/ root@$Sensai:/etc/postfix/
      rsync -razP /etc/aliases root@$Sensai:/etc/aliases
      ## REVERSE Logs
      rsync -azP /var/log/ root@$Sensai:/var/log/
      rsync -azP /etc/logrotate.d/ root@$Sensai:/etc/logrotate.d/
      ## REVERSE SYSTEM-CRON
      rsync -razP /etc/crontab root@$Sensai:/etc/crontab
      ## REVERSE ALIASES
      rsync -razP /etc/bash.bashrc root@$Sensai:/etc/bash.bashrc
      ## REVERSE USERS
      rsync -azP /root/ root@$Sensai:/root/ --exclude=.ssh/known_hosts  --exclude=master-sync/
      rsync -azP /home/ root@$Sensai:/home/ /home/
      rsync -azP /etc/shadow root@$Sensai:/etc/shadow
      rsync -azP /etc/shadow- root@$Sensai:/etc/shadow-
      rsync -azP /etc/passwd root@$Sensai:/etc/passwd
      rsync -azP /etc/passwd- root@$Sensai:/etc/passwd-
      rsync -azP /etc/group root@$Sensai:/etc/group
      rsync -azP /etc/group- root@$Sensai:/etc/group-
      rsync -azP /etc/gshadow root@$Sensai:/etc/gshadow
      rsync -azP /etc/gshadow- root@$Sensai:/etc/gshadow-

      ## DEMOTE TO 3ASKARY ME3AFEN
      echo 0 > PROMOTE
  fi
else
  echo " ##########################################  MY SENSAI IS DEAD.... I AM THE NEW MASTER !"
  echo 1 > PROMOTE
fi

}}}

== TACACS Active Sync ==

{{{
 /bin/bash       <--- Parser

Sensai=172.17.13.37

## SYNCING TACACS
rsync -azP root@$Sensai:/etc/tacacs+/ /etc/tacacs+/
rsync -azP root@$Sensai:/var/log/ /var/log/
rsync -azP root@$Sensai:/etc/logrotate.d/ /etc/logrotate.d/

## SYNCING USERS
rsync -azP root@$Sensai:/root/ /root/ --exclude=.ssh/known_hosts
rsync -azP root@$Sensai:/home/ /home/
rsync -azP root@$Sensai:/etc/shadow /etc/shadow
rsync -azP root@$Sensai:/etc/shadow- /etc/shadow-
rsync -azP root@$Sensai:/etc/passwd /etc/passwd
rsync -azP root@$Sensai:/etc/passwd- /etc/passwd-
rsync -azP root@$Sensai:/etc/group /etc/group
rsync -azP root@$Sensai:/etc/group- /etc/group-
rsync -azP root@$Sensai:/etc/gshadow /etc/gshadow
rsync -azP root@$Sensai:/etc/gshadow- /etc/gshadow-
}}}

= Conclusion =

Rsync can simplify file transfers over networked connections and add robustness to local directory syncing. The flexibility of rsync makes it a good option for many different file-level operations.

A mastery of rsync allows you to design complex backup operations and obtain fine-grained control over what is transferred and how.
