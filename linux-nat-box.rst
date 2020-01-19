{{{
Author       : Mohamed Ashraf
Title        : Linux Natter Box Module
Version      : 1.0
Creation date: 23/11/2014
Latest update: 23/11/2014
}}}

<<TableOfContents(4)>>


{{attachment:nat.jpg}}



= Configuration =

To setup a Linux Router/Nat box, you have to go through below

Since you are Natting between two subnets, the Natter box needs to have two working interfaces.

A Typical Configuration can be as below

'''@/etc/network/interfaces'''

{{{
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
        address 192.168.0.72
        netmask 255.255.255.0
        broadcast 192.168.0.255
        gateway 192.168.0.1
        dns-nameservers 127.0.0.1

auto eth1
iface eth1 inet static
        address 10.10.10.11
        netmask 255.255.255.0

}}}

- As noticed, for the internal network, no Gateway is needed.

- DNS can be configured as desired, in above case the DNS server was local.

= Iptables and traffic forwarding =

To save configuration Time, we have applied below script to do all the work needed for adjusting IPtables.

Copy over below contents of the script and run it as root

{{{
echo -e "\n\nLoading simple rc.firewall-iptables version $FWVER..\n"
DEPMOD=/sbin/depmod
MODPROBE=/sbin/modprobe

EXTIF="eth0"
INTIF="eth1"
#INTIF2="eth0"
echo "   External Interface:  $EXTIF"
echo "   Internal Interface:  $INTIF"

#======================================================================
#== No editing beyond this line is required for initial MASQ testing == 
echo -en "   loading modules: "
echo "  - Verifying that all kernel modules are ok"
$DEPMOD -a
echo "----------------------------------------------------------------------"
echo -en "ip_tables, "
$MODPROBE ip_tables
echo -en "nf_conntrack, " 
$MODPROBE nf_conntrack
echo -en "nf_conntrack_ftp, " 
$MODPROBE nf_conntrack_ftp
echo -en "nf_conntrack_irc, " 
$MODPROBE nf_conntrack_irc
echo -en "iptable_nat, "
$MODPROBE iptable_nat
echo -en "nf_nat_ftp, "
$MODPROBE nf_nat_ftp
echo "----------------------------------------------------------------------"
echo -e "   Done loading modules.\n"
echo "   Enabling forwarding.."
echo "1" > /proc/sys/net/ipv4/ip_forward
echo "   Enabling DynamicAddr.."
echo "1" > /proc/sys/net/ipv4/ip_dynaddr 
echo "   Clearing any existing rules and setting default policy.."

iptables-restore <<-EOF
*nat
-A POSTROUTING -o "$EXTIF" -j MASQUERADE
COMMIT
*filter
:INPUT ACCEPT [0:0]
:FORWARD DROP [0:0]
:OUTPUT ACCEPT [0:0]
-A FORWARD -i "$EXTIF" -o "$INTIF" -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT 
-A FORWARD -i "$INTIF" -o "$EXTIF" -j ACCEPT
-A FORWARD -j LOG
COMMIT
EOF

echo -e "\nrc.firewall-iptables v$FWVER done.\n"
}}}


= Boot Inilization =

- In order for the Box to retain it's Natting Configuration after server reboot, apply the below

''1- Name the script "nat.sh", and copy it over to /etc/init.d/''

{{{
cp nat.sh /etc/init.d/
}}}

2- ''Make sure it is owned by root''

{{{
chown root:root /etc/init.d/nat.sh
}}}

3- ''Make it executable''

{{{
chmod a+x /etc/init.d/nat.sh
}}}

4- Initialize the script, so the kernel runs it at boot

{{{
update-rc.d nat.sh defaults
}}}


= Case Use =

A Typical topology for the natter box is as illustrated below

{{attachment:1.jpg}}
