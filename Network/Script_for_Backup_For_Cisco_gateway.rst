{{{
Author       : Mahmoud Hamdy
Version      : 1.1
Creation date: 2014-11-27
Latest update: 2014-11-27
}}}

<<TableOfContents(3)>>

= Linux script for backup configuration file of our gateway =

{{{
( sleep 10
echo "enable"
sleep 1
echo "password"
sleep 1
echo "copy running-config scp:"
sleep 5
echo "192.168.0.80"
sleep 5
echo "root "
sleep 5
echo "CISCOBACKUP "
sleep 5
echo "password"
sleep 5
echo "exit" ) | telnet 192.168.0.1
sleep 5
cp /root/CISCOBACKUP /backup/network/Cisco/

}}}
