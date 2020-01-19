{{{
Author       : Mohamed Ashraf
Title        : Asynchronous Results Collector (A.R.C) : External Quota counting Solution
Version      : 1.0
Creation date: 25/2/2015
Latest update: 25/2/2015
}}}



<<TableOfContents(4)>>



{{attachment:logo.jpg}}


= Introduction =

- At some point, arised the need for a simple tool that can calculate quota usage of certain links.

- In order to address this need, A.R.C was created.

= A.R.C Core =

'''A.R.C Depends on two main daemons:'''

- The Data-Collector: which is active all day collecting usage information via SNMP.

- The Parser: Which operates at the end of the day, to map, calculate & convert SNMP usage collected to Quota Usage in terms of bandwidth.


= Building A.R.C =

- Below are the main components of A.R.C, that all work in a flow each passing variables to the next.

== Flow1-CORE.fetch-data ==

{{{
 /bin/bash  <-- Parser, precede with #!



###################### FETCH DATA & DO STUFF BY MOHAMED ASHRAF

### SINCE 32 BIT COUNTERS:
#32-bit = 2^32-1 = 4294967295 MAX-VALUE
#64-bit = 2^64-1 = 18446744073709551615 MAX-VALUE
# IN 32     : 1.3.6.1.2.1.2.2.1.10.INDEX
# OUT 32    : 1.3.6.1.2.1.2.2.1.16.INDEX
# IN 64     : 1.3.6.1.2.1.31.1.1.1.6.INDEX
# OUT 64    : 1.3.6.1.2.1.31.1.1.1.10.INDEX
# CISCO SIM : 1.3.6.1.4.1.9.9.661.1.3.1.1.3.INDEX
# Carrier   : 1.3.6.1.4.1.9.9.661.1.3.2.1.9.INDEX

#Env-init
PWD=/home/zenoss/Quota-Check
cd /home/zenoss/Quota-Check

######## VANOORD
## Vivaldi
index=$(snmpwalk -v2c -cvanoord 10.77.6.46 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.6.46 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.46 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/Vanrood1.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.46 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/Vanrood1.OUT.wm-0.RAW

index=$(snmpwalk -v2c -cvanoord 10.77.6.50 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.6.50 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.50 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/Vanrood2.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.50 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/Vanrood2.OUT.wm-0.RAW

index=$(snmpwalk -v2c -cvanoord 10.77.6.54 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.6.54 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.54 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/Vanrood3.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.54 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/Vanrood3.OUT.wm-0.RAW

index=$(snmpwalk -v2c -cvanoord 10.77.6.58 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.6.58 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.58 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/Vanrood4.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.6.58 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/Vanrood4.OUT.wm-0.RAW

## Head Quarter
index=$(snmpwalk -v2c -cvanoord 10.77.5.194 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.5.194 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.194 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/VanroodHQ1.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.194 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/VanroodHQ1.OUT.wm-0.RAW

index=$(snmpwalk -v2c -cvanoord 10.77.5.202 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.5.202 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.202 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/VanroodHQ2.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.202 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/VanroodHQ2.OUT.wm-0.RAW

index=$(snmpwalk -v2c -cvanoord 10.77.5.206 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.5.206 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.206 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/VanroodHQ3.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.206 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/VanroodHQ3.OUT.wm-0.RAW

index=$(snmpwalk -v2c -cvanoord 10.77.5.210 ifdesc | grep wm0 | cut -d " " -f1 | cut -d "." -f2)
if [ -z "$index" ];
then
index=$(snmpwalk -v2c -cvanoord 10.77.5.210 ifdesc | grep eth0 | cut -d " " -f1 | cut -d "." -f2)
fi
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.210 1.3.6.1.2.1.31.1.1.1.6.$index)
echo $curvalue >> temp/VanroodHQ4.IN.wm-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -cvanoord 10.77.5.210 1.3.6.1.2.1.31.1.1.1.10.$index)
echo $curvalue >> temp/VanroodHQ4.OUT.wm-0.RAW

######## PUCCINI
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.10.5)
echo $curvalue >> temp/Puccini.IN.cell0-0-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.16.5)
echo $curvalue >> temp/Puccini.OUT.cell0-0-0.RAW


IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.10.7)
echo $curvalue >> temp/Puccini.IN.cell0-1-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.16.7)
echo $curvalue >> temp/Puccini.OUT.cell0-1-0.RAW

IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.10.9)
echo $curvalue >> temp/Puccini.IN.cell0-2-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.16.9)
echo $curvalue >> temp/Puccini.OUT.cell0-2-0.RAW

IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.10.11)
echo $curvalue >> temp/Puccini.IN.cell0-3-0.RAW
IFS=":" read dumb1 dumb2 dumb3 curvalue < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.2.1.2.2.1.16.11)
echo $curvalue >> temp/Puccini.OUT.cell0-3-0.RAW

### OPTIMIZE RAW FOR PARSING
cd /home/zenoss/Quota-Check/temp/

for caching in *.RAW
do
  grep -v '^$' $caching > "${caching%.RAW}.cache"
  mv "${caching%.RAW}.cache" $caching
done

### Optimizing Time Ticks

#for TikTok in *.TICKS
#do
#  cat $TikTok | cut -d "(" -f2 | cut -d ")" -f1 > "${caching%.TICKS}.cache"
#  mv "${caching%.TICKS}.cache" $TikTok
#done


# TO SUM
#awk '{ sum += $1 } END { print sum }' customer1.RAW
}}}

== Flow2-knowing.Sim-Data ==

{{{
 /bin/bash  <-- Parser, precede with #!

###################### FETCH SIM DATA & DO DB BY MOHAMED ASHRAF

### SINCE 32 BIT COUNTERS:
#32-bit = 2^32-1 = 4294967295 MAX-VALUE
#64-bit = 2^64-1 = 18446744073709551615 MAX-VALUE
# IN 32     : 1.3.6.1.2.1.2.2.1.10.INDEX
# OUT 32    : 1.3.6.1.2.1.2.2.1.16.INDEX
# IN 64     : 1.3.6.1.2.1.31.1.1.1.6.INDEX
# OUT 64    : 1.3.6.1.2.1.31.1.1.1.10.INDEX
# CISCO SIM : 1.3.6.1.4.1.9.9.661.1.3.1.1.3.INDEX
# Carrier   : 1.3.6.1.4.1.9.9.661.1.3.2.1.9.INDEX

#Env-init
PWD=/home/zenoss/Quota-Check
cd /home/zenoss/Quota-Check

########### PUCCINI
IFS=":" read dumb1 dumb2 dumb3 carrier < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.2.1.9.31)
echo $carrier > temp/Puccini.cell0-0-0.carrier
IFS=":" read dumb1 dumb2 dumb3 serial < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.1.1.3.31)
echo $serial > temp/Puccini.cell0-0-0.serial

IFS=":" read dumb1 dumb2 dumb3 carrier < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.2.1.9.32)
echo $carrier > temp/Puccini.cell0-1-0.carrier
IFS=":" read dumb1 dumb2 dumb3 serial < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.1.1.3.32)
echo $serial > temp/Puccini.cell0-1-0.serial

IFS=":" read dumb1 dumb2 dumb3 carrier < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.2.1.9.33)
echo $carrier > temp/Puccini.cell0-2-0.carrier
IFS=":" read dumb1 dumb2 dumb3 serial < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.1.1.3.33)
echo $serial > temp/Puccini.cell0-2-0.serial

IFS=":" read dumb1 dumb2 dumb3 carrier < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.2.1.9.34)
echo $carrier > temp/Puccini.cell0-3-0.carrier
IFS=":" read dumb1 dumb2 dumb3 serial < <(snmpget -v2c -c n00r 172.16.252.1 1.3.6.1.4.1.9.9.661.1.3.1.1.3.34)
echo $serial > temp/Puccini.cell0-3-0.serial

########## Vanoord
## Vivaldi
IP=10.77.6.46
NAME=Vivaldi1
echo $NAME > temp/Vanrood1.wm-0.carrier
echo $IP > temp/Vanrood1.wm-0.serial

IP=10.77.6.50
NAME=Vivaldi2
echo $NAME > temp/Vanrood2.wm-0.carrier
echo $IP > temp/Vanrood2.wm-0.serial

IP=10.77.6.54
NAME=Vivaldi3
echo $NAME > temp/Vanrood3.wm-0.carrier
echo $IP > temp/Vanrood3.wm-0.serial

IP=10.77.6.58
NAME=Vivaldi4
echo $NAME > temp/Vanrood4.wm-0.carrier
echo $IP > temp/Vanrood4.wm-0.serial

## HQ
IP=10.77.5.194
NAME=HQ1
echo $NAME > temp/VanroodHQ1.wm-0.carrier
echo $IP > temp/VanroodHQ1.wm-0.serial

IP=10.77.5.202
NAME=HQ2
echo $NAME > temp/VanroodHQ2.wm-0.carrier
echo $IP > temp/VanroodHQ2.wm-0.serial

IP=10.77.5.206
NAME=HQ3
echo $NAME > temp/VanroodHQ3.wm-0.carrier
echo $IP > temp/VanroodHQ3.wm-0.serial

IP=10.77.5.210
NAME=HQ4
echo $NAME > temp/VanroodHQ4.wm-0.carrier
echo $IP > temp/VanroodHQ4.wm-0.serial
}}}


== Flow3-THEORY.Check-Ticks ==

{{{
 /bin/bash  <-- Parser, precede with #!


######## CHAOS THEORY BY MOHAMED ASHRAF

## ENV INIT
PWD=/home/zenoss/Quota-Check/temp
cd /home/zenoss/Quota-Check/temp
cat /dev/null > Customer-Ifaces.DB

### OPTIMIZE RAW FOR PARSING
for caching in *.RAW
do
  grep -v '^$' $caching > "${caching%.RAW}.cache"
  mv "${caching%.RAW}.cache" $caching
done

## LETS DO SOME LOGIC
for raw in *.RAW
do
  echo "Processing $raw"
  customer=$(echo -en $raw | cut -d "." -f1)
  iface=$(echo $raw | cut -d "." -f3)

  if [[ $raw == *"OUT"* ]]
  then
      echo -en $customer                            >> Customer-Ifaces.DB
      echo -en ":"                                  >> Customer-Ifaces.DB
      echo $iface                                   >> Customer-Ifaces.DB
  fi

  chaos=$raw                              #MASTER TRIGGER
  FirstBlood=$(head -1 $chaos)
  PentaKill=$(tail -1 $chaos)
  if [ -z "$FirstBlood" ];
  then
    let FirstBlood=0
  fi
    if [ -z "$PentaKill" ];
  then
     let PentaKill=0
  fi
  pre=0
  Nreset=0
  cat /dev/null > "${raw%.RAW}.TICKS-OFFSET"

  while read entry; do
    post=$entry
    if [ "$post" -lt "$pre" ]
    then
        let Nreset=$Nreset+1
        let session=$pre-$FirstBlood
        echo $session >> "${raw%.RAW}.TICKS-OFFSET"
        echo $post >> "${raw%.RAW}.TICKS-OFFSET"
        FirstBlood=$post
    fi
    pre=$entry
  done < $chaos

  if [ "$Nreset" == "0" ]
  then
      echo "NO RESETS OCCURED"
      let TOTAL=$PentaKill-$FirstBlood
      echo $TOTAL > "${raw%.RAW}.TOTAL"
  else
      echo "$Nreset RESET(s) OCCURED"
#     let TOTAL=($Nreset*4294967295)+$PentaKill
      let TOTAL=$(awk '{ sum += $1 } END { print sum }' "${raw%.RAW}.TICKS-OFFSET")+$PentaKill
      echo $TOTAL > "${raw%.RAW}.TOTAL"
  fi
done

## Calculating Interface TOTAL
while read pointer; do
     customer=$(echo $pointer | cut -d ":" -f1)
     iface=$(echo $pointer | cut -d ":" -f2)
     awk '{ sum += $1 } END { print sum }' $customer.*.$iface.TOTAL > $customer.IFACE.$iface.TOTAL
done < Customer-Ifaces.DB
}}}

== Flow4-PARSE.Converting-data ==

{{{
 /bin/bash  <-- Parser, precede with #!

######## Data Convertion BY MOHAMED ASHRAF

## ENV INIT
PWD=/home/zenoss/Quota-Check/temp
cd /home/zenoss/Quota-Check/temp

# GIGA = 1073741824
# MEGA = 1048576
# KILO = 1024

## Convert da DETA
for IFACES in *.IFACE.*.TOTAL
do
  bytesSTR=$(cat $IFACES)
  byte=${bytesSTR/.*}
  if [ "$byte" -gt "1073741824" ]
  then
     bytes=$(bc -l <<< "scale=3; $byte/1073741824")
     converted="$bytes GB"
     echo $converted                     > "${IFACES%.TOTAL}.REPORT"
  elif [ "$byte" -gt "1048576" ]
  then
     bytes=$(bc -l <<< "scale=3; $byte/1048576")
     converted="$bytes MB"
     echo $converted                     > "${IFACES%.TOTAL}.REPORT"
  elif [ "$byte" -gt "1024" ]
  then
     bytes=$(bc -l <<< "scale=3; $byte/1024")
     converted="$bytes KB"
     echo $converted                     > "${IFACES%.TOTAL}.REPORT"
  elif [ "$byte" -lt "1024" ]
  then
     converted="$bytes Bytes"
     echo $converted                     > "${IFACES%.TOTAL}.REPORT"
  fi

done
}}}

== Flow5-REPORT.creating-report ==

{{{
 /bin/bash  <-- Parser, precede with #!

################### Reporting the Reported Report By Mohamed Ashraf

## ENV INIT
PWD=/home/zenoss/Quota-Check/temp
cd /home/zenoss/Quota-Check/temp
cat /dev/null                                            > Customer-Ifaces.FULL.DB

############## Combining uni-data
while read pow;do

    CUname=$(echo $pow | cut -d ":" -f1)                     # CUSTOMER
    IFname=$(echo $pow | cut -d ":" -f2)                     # IFACE
    CAnameTemp=$(cat $CUname.$IFname.carrier)
    SEnameTemp=$(cat $CUname.$IFname.serial)
    CAname=$(echo $CAnameTemp | cut -d '"' -f2)             # CARRIER
    SEname=$(echo $SEnameTemp | cut -d '"' -f2)             # SERIAL

    echo "$pow        $CAname:$SEname"                  >> Customer-Ifaces.FULL.DB

done < Customer-Ifaces.DB

############## Fetching uni-reports
let whack=1
echo "-----------------------------------------------------" > THE-REPORT-TODAY.PRE
for ZAP in *.REPORT
do
  customer=$(echo -en $ZAP | cut -d "." -f1)
  iface=$(echo $ZAP | cut -d "." -f3)

  sed -n ""$whack"p" Customer-Ifaces.FULL.DB           >> THE-REPORT-TODAY.PRE
  echo -en "Usage of Today: "                          >> THE-REPORT-TODAY.PRE
  cat $customer.IFACE.$iface.REPORT                    >> THE-REPORT-TODAY.PRE
  echo "-----------------------------------------------------" >> THE-REPORT-TODAY.PRE

  let whack=$whack+1
done


#### Mail Construction
echo "From: ARC@noor.net"                                                                  > THE-REPORT-TODAY.READY
echo "To: mashraf@noor.net"                                                               >> THE-REPORT-TODAY.READY
echo "To: crm@noor.net"                                                                   >> THE-REPORT-TODAY.READY
echo "Subject: [A.R.C] 3G External Quota STATUS                  "                        >> THE-REPORT-TODAY.READY
echo                                                                                      >> THE-REPORT-TODAY.READY
echo "                                 Asynchronous Results Collector [A.R.C]"            >> THE-REPORT-TODAY.READY
echo "                                     Daily 3G External Quota STATUS"                >> THE-REPORT-TODAY.READY
echo                                                                                      >> THE-REPORT-TODAY.READY
echo $(date +"%A %d %B %Y [%r]")                                                          >> THE-REPORT-TODAY.READY
echo                                                                                      >> THE-REPORT-TODAY.READY
cat THE-REPORT-TODAY.PRE                                                                  >> THE-REPORT-TODAY.READY

#### Sending the Report
/usr/sbin/ssmtp -t < THE-REPORT-TODAY.READY
}}}


= A.R.C Engine =

- After building all A.R.C Flow, the Two master Daemons that operates them needs to be built as well:

== D1-OPENFIRE ==

{{{
 /bin/bash  <-- Parser, precede with #!

################### D1-Link By Mohamed Ashraf

## ENV INIT
PWD=/home/zenoss/Quota-Check/
cd /home/zenoss/Quota-Check/

./Flow1-CORE.fetch-data
}}}

== D2-IGNITE  ==

{{{
 /bin/bash  <-- Parser, precede with #!


################### D2-Link By Mohamed Ashraf

## ENV INIT
PWD=/home/zenoss/Quota-Check/
cd /home/zenoss/Quota-Check/

## Stream the flow
./Flow2-knowing.Sim-Data
./Flow3-THEORY.Check-Ticks
./Flow4-PARSE.Converting-data
./Flow5-REPORT.creating-report

## Reset the Day
cd /home/zenoss/Quota-Check/temp/
rm *
}}}

== Cron it! ==

- Laslty we cron command the daemons to operate at the times of our choosing:

''crontab''
{{{

*/2 * * * * /home/zenoss/Quota-Check/D1-OPENFIRE                 <--- Every 2 minutes
0 0 * * * /home/zenoss/Quota-Check/D2-IGNITE                     <--- At 12:00 AM

}}}


= A.R.C Report Example =

- Example Mail report for Quota Usage:

{{{
                                  Asynchronous Results Collector [A.R.C]
                                     Daily 3G External Quota STATUS

Wednesday 25 February 2015 [12:00:04 AM]

-----------------------------------------------------
Puccini:cell0-0-0        etisalat:8920030114442657048
Usage of Today: 3.869 GB
-----------------------------------------------------
Puccini:cell0-1-0        etisalat:8920030114442657052
Usage of Today: 2.805 GB
-----------------------------------------------------
Puccini:cell0-2-0        etisalat:8920030114442657049
Usage of Today: 9.240 GB
-----------------------------------------------------
Puccini:cell0-3-0        etisalat:8920030114442656984
Usage of Today: 4.891 GB
-----------------------------------------------------
Vanrood1:wm-0        Vivaldi1:10.77.6.46
Usage of Today: 3.191 GB
-----------------------------------------------------
Vanrood2:wm-0        Vivaldi2:10.77.6.50
Usage of Today: 57.004 GB
-----------------------------------------------------
Vanrood3:wm-0        Vivaldi3:10.77.6.54
Usage of Today: 65.164 GB
-----------------------------------------------------
Vanrood4:wm-0        Vivaldi4:10.77.6.58
Usage of Today: 39.618 GB
-----------------------------------------------------
VanroodHQ1:wm-0        HQ1:10.77.5.194
Usage of Today: 18.853 GB
-----------------------------------------------------
VanroodHQ2:wm-0        HQ2:10.77.5.202
Usage of Today: 4.514 GB
-----------------------------------------------------
VanroodHQ3:wm-0        HQ3:10.77.5.206
Usage of Today: 6.344 GB
-----------------------------------------------------
VanroodHQ4:wm-0        HQ4:10.77.5.210
Usage of Today: 7.163 GB
-----------------------------------------------------
}}}
