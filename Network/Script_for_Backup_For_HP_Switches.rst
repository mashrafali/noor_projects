{{{
Author       : Mahmoud Hamdy
Version      : 1.1
Creation date: 2014-11-27
Latest update: 2014-11-27
}}}

<<TableOfContents(3)>>

= Linux script for backup configuration file of our HP Switches in City Stars =


{{{
( sleep 5
echo "it"
sleep 1
echo "it@n00r;"
sleep 1
echo "copy running-config sftp it@192.168.0.80 HPbackup"
sleep 5
echo "PASSWORD"
sleep 10
echo "exit"
sleep 1
echo "exit"
echo " "
sleep 1
echo "exit"  ) | telnet 192.168.0.156
sleep 5
cp HPbackup /backup/network/HP/HPBACKUP156-serverroom

}}}
