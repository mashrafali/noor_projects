{{{
Author       : Mahmoud Hamdy
Version      : 1.1
Creation date: 2014-11-27
Latest update: 2014-11-27
}}}

<<TableOfContents(3)>>

= Linux script for backup configuration file of our DELL Switches in Ramsis =

{{{
( sleep 10
echo "root"
sleep 1
echo "password"
sleep 1
echo "enable"
sleep 1
echo "password"
sleep 1
echo "show run"
sleep 1

i=18

while (i -ge 0)
do
echo " "
sleep 1

i--
done

echo "exit"  ) | telnet 172.17.12.6
}}}
