{{{
Author       : Mohamed Ashraf
Title        : Kerberos Backup & Active Synchronization
Version      : 1.0
Creation date: 25/2/2015
Latest update: 25/2/2015
}}}



<<TableOfContents(4)>>



{{attachment:logo.png}}

= Introduction =

- Almost all Engineering teams uses the Kerberos server to access our Nodes (Ps , PEs, AGGs,..)

- For this reason it is critical to keep to keep this sever up and running at all times.

- Under this umbrella, a High availability solution has been designed for our kerberos.

= Solution Built =

- The following steps has been undertaken to secure our kerberos uptime.

== Building a Slave ==

- The running Kerberos has been renamed '''Kerberos-Master'''. [10.9.9.222]

- Another Reblica has been built to act as a Passive HA node, the slave server has been named '''Kerberos-Slave'''. [10.9.9.111]

== The Fail-over ==

- The failover solution was preferred to be configured from the routers side, as they are the most sensitive to their authentication servers.

== Active Synchronization ==

- The following Module has been used for Active-Sync between the Master and the Slave.
http://wiki/Enterprise/IT/Docs/HowTo/Linux/active-sync

== The Configuration applied ==

'''First''', We created a directory to host our sync-trigger operation:

''pwd''
{{{
/root/master-sync
}}}

''LS''
{{{
root@Kerberos-Slave:~/master-sync# ls
master-sync  PROMOTE
}}}

'''Second''', We created the Sync-trigger Script:

{{{
#! /bin/bash

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


'''Third''', We created a minute by minute check and sync crontab command.

''crontab''
{{{

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

* * * * * /usr/bin/pydf > /root/banner

*/10 * * * * /root/master-sync/master-sync
}}}


'''Last''', We copied over Slave's RSA-key to it's Master, so it can ssh sync to the master without using any passwords.

''Create the key''
{{{
root@Kerberos-Slave:~# ssh-keygen -t rsa

Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 root@Kerberos-Slave
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
}}}

''Copy it to master''
{{{
root@Kerberos-Slave:~# ssh-copy-id root@10.9.9.222
}}}
