{{{
Author       : Mohamed Ashraf
Title        : FortiGate UTM Troubleshooting, Tips & Tricks
Version      : 1.0
Creation date: 27/8/2015
Latest update: 27/8/2015
}}}


<<TableOfContents(4)>>



{{attachment:logo1.jpeg}}


= Global Packet Sniffer Direct URL =

{{{
http://172.17.13.200/p/firewall/sniffer/
}}}


= Reach FortiCloud for license activation =

{{{
execute update-now
}}}

= Format Local Log Disk =

{{{
execute formatlogdisk 
}}}

= Config how the box connects to FortiGuard =

{{{
config system fortiguard
    set source-ip x.x.x.x
end
}}}

- To Debug this command:

{{{
diag debug app update -1
diag debug enable
exec update-now

-- ##After you finish ##

diagnose debug disable 
}}}

= Config System storage size =

* Not Active (Ticket Needs to be opened for this issue)

{{{
config global
config system storage
set size 30236           <--30 Gigs Value
}}}

= FortiGate BOX Troubleshooting Status Commands =

{{{
get sys status 
get sys ha status 
diag sys top 1 20(run for 5 seconds and Ctrl+C to stop) 
get sys perf status 
diag hard sysinfo shm 
diagnose hardware sysinfo memory 
diag debug crashlog read 
exec tac report 
diagnose debug config-error-log read 
diagnose log alertconsole list 
diag sys flash list 
diag autoupdate versions
}}}

= Configure NTP Sync Source IP =

{{{
NOOR-FG-1 # config global 

NOOR-FG-1 (global) # config system ntp 

NOOR-FG-1 (ntp) # show full-configuration 
config system ntp
    set ntpsync enable
    set type custom
    set syncinterval 60
        config ntpserver
            edit 1
                set server "217.139.253.1"
                set ntpv3 disable
            next
        end
    set source-ip 41.187.9.93
    set server-mode disable
end
}}}

= UnderLay Free-BSD Linux Server Contact =

{{{
fnsysctl
}}}

- example

{{{
NOOR-FG-1 # fnsysctl df -h
Filesystem                 Size       Used  Available Use% Mounted on
rootfs                     5.8G      77.0M       5.7G   1% /
tmpfs                      5.8G      77.0M       5.7G   1% /
none                      11.2G     113.9M      11.1G   1% /tmp
none                      11.2G     417.2M      10.8G   4% /dev/shm
none                      11.2G      47.8M      11.2G   0% /dev/cmdb
/dev/sda1                123.9M      38.1M      79.4M  32% /data
/dev/sda2                  1.8G      19.6M       1.7G   1% /data2
/dev/sdb1                 29.5G     615.3M      27.4G   2% /var/log
/dev/sdb1                 29.5G     615.3M      27.4G   2% /var/storage/Virtual-Disk-3913481F25AAE935
}}}


= Enable SCP Client to the FortiGate Unit =

- To enable SCP from the FortiGate Unit, mainly for Scheduled Config Backups
{{{
config system global
  set admin-scp enable
end
}}}

= Some More Tricks =

[[attachment:CLI Tips & Tricks.pdf]]


= Links to useful Fortinet Resources =

[[http://kb.fortinet.com/kb/microsites/microsite.do| Knowledge Base]]

[[http://www.fortinet.com/resource_center/datasheets.html| Products Datasheet]]

[[http://docs.fortinet.com/p/inside-fortios| Inside FortiOS]]

[[http://www.fortinet.com/products/index.html| Fortinet Products]]

[[http://www.fortinet.com/products/index.html| Fortinet Technical Documentation]]

[[http://docs.fortinet.com/fortigate/cookbook| FortiOS Cookbook]]  

[[http://video.fortinet.com/| Fortinet Video Library]]

[[http://video.fortinet.com/| FortiGuard]]

[[https://fuse.fortinet.com/| Fortinet Fuse Community]] 

[[http://www.fortidemo.com/| Demo Center username/password: demo/demo]] 
