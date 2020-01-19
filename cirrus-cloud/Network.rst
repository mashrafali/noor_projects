{{{
Author       : Mohamed Ashraf
Title        : CIRRUS-SDN Node Deployment
Creation date: 18/10/2015
Latest update: 18/10/2015
}}}

{{attachment:logo-c3.jpg}}

<<TableOfContents(4)>>

= Preparing System =

- First off, you need to apply the following to prepare the system for Cirrus-SDN.

== Updating System ==

- Fully update the system Packages and kernel before you begin doing anything.

- Apply the following command to update all

{{{
apt-get update && apt-get upgrade -y && apt-get dist-upgrade -y && apt-get --purge autoremove -y && apt-get autoclean
}}}


== Network Configuration ==

- Configure Network interfaces of the Node, by setting Static IPs to each required Interface.

- Typical Network Configuration for SDN Node is as below

{{{
# The loopback network interface
auto lo
iface lo inet loopback

## MANGMENT NETWORK
auto p2p1
iface p2p1 inet static
        address 192.168.0.131
        netmask 255.255.255.0
        gateway 192.168.0.1
        dns-nameservers 192.168.0.4
        dns-search noor.net

## TUNNEL NETWORK
auto p1p1
iface p1p1 inet static
        address 10.0.1.1
        netmask 255.255.255.0

## External NETWORK
auto p1p2
iface p1p2 inet manual
        pre-up /sbin/ethtool -K $IFACE gro off
        up ip link set dev $IFACE up
        up ip link set $IFACE promisc on
        down ip link set dev $IFACE down
        down ip link set $IFACE promisc off
}}}

- After finishing Above two Steps, you need to '''REBOOT'''.





= Deployment Script =

- Copy & Paste Below Script in the system.

- Replace the Ports that u need to Configure in the "CONFIGURE USED PORTS" inside the script  ex :{port0=eth0, port0=eth1,...}

- Configure the IP Address of the controller node in the "CONFIGURE NODES" Section.

- Run as root to Deploy Cirrus-SDN Node.

- The script will confirm the used Network Interfaces before it begins.

{{{
! /bin/bash         <- PASRER, PRECEDE WITH "#"
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games

##################### SCRIPT CONFIGURATION
#CONFIGURE NODES:
###
controllerip=192.168.0.130
###
#CONFIGURE USED PORTS:
###
port0=eth0            #Mangment Network
port1=eth1            #Tunnel Network
port2=eth2            #External Network
####################################################

MYIP0=$(ifconfig $port0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
MYIP1=$(ifconfig $port1 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo -en "                                         " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack SDN Auto Deployment   "
echo "                                                  Script by Mohamed Ashraf "
echo
echo
echo " -> Please run this script as root! "
echo
echo " Parameters INFO:"
echo "-----------------"
echo " - Mangment Network IP will be used from $port0 : $MYIP0"
echo " - Tunnel Network IP will be used from $port1 : $MYIP1"
echo " - External Public Network IP will be used from $port2 : Manual Configuration"
echo
echo " >>>>> Hit Enter when you are Ready <<<<<"
read
sleep 2
echo " #################################### PREPARING SYSTEM ############################"
sleep 1
echo
echo -en "Configuring Apt-Cache Server Link..."
sleep 2
echo '
Acquire::http { Proxy "http://192.168.0.114:3142"; };
' > /etc/apt/apt.conf.d/02apt-cacher
echo "[OK]"
echo
sleep 1
apt-get update
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get --purge autoremove -y
apt-get autoclean
apt-get install ssh htop pydf unzip iftop snmpd snmp make nano tcpdump gcc sudo dnsutils ntp ethtool -y
apt-get dist-upgrade -y
apt-get upgrade -y
apt-get --purge autoremove -y
apt-get autoclean
echo
echo
echo " #################################### Optimizing Swap ####################################"
sleep 1
echo
echo " Configuring System Not to Swap unless to avoid System out of memory....."
sleep 2
echo
echo -en " "
echo "vm.swappiness = 0" >> /etc/sysctl.conf
sysctl -w vm.swappiness=0
echo
sleep 2
echo " Configuration OK!"
echo
echo
echo " #################################### Finalizing Environment  ############################"
sleep 2
echo
echo -en " Configuring network management services....."
cd /etc/snmp/
cp snmpd.conf snmpd.conf.BACKUP

echo "
#  Listen for connections from the local system only
agentAddress  udp:127.0.0.1:161
#  Listen for connections on eth0 only
agentAddress  udp:$MYIP:161

## System location and contact information
syslocation NOOR
syscontact NOOR-SYSTEMS

rocommunity N0oRiNfR@
" > snmpd.conf
sleep 1
echo "[OK]"
echo
echo -en " "
echo -en $(/etc/init.d/snmpd restart)
sleep 1
echo "....[OK]"
echo
echo -en " Applying new HOSTNAME....."
echo Cirrus-SDN > /etc/hostname
sleep 1
echo "[OK]"
echo
echo -en " Configuring HOSTS File....."
echo "
127.0.0.1       localhost.localdomain localhost
$MYIP0          Cirrus-SDN.noor.net Cirrus-SDN

#Cirrus-OpenStack Nodes
$controllerip     Cirrus-Controller.noor.net Cirrus-Controller
$MYIP0            Cirrus-SDN.noor.net Cirrus-SDN

" > /etc/hosts
sleep 1
echo "[OK]"
echo
echo -en " Adjusting SSH Parameters....."
sed -i 's#PermitRootLogin without-password#PermitRootLogin yes#g' /etc/ssh/sshd_config
sed -i 's/#GSSAPIAuthentication no/GSSAPIAuthentication no/g' /etc/ssh/sshd_config 
echo "
#Special Config Parameters For Fast SSH
UseDNS no
" >> /etc/ssh/sshd_config
sleep 1
echo "[OK]"
echo
echo
echo -en " #################################### Preparing " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack SDN Environment ############################"
echo
echo -en " Configuring NTP Server....."
sleep 1
sed -i 's/server /#server /g' /etc/ntp.conf
echo "server Cirrus-Controller" >> /etc/ntp.conf
echo "[OK]"
echo
echo -en " Adjusting Hostname..."
sleep 1
hostname Cirrus-SDN
echo "[OK]"
echo
echo "--->> Adding OpenStack KeyRing & repository..."
sleep 1
echo
apt-get install ubuntu-cloud-keyring -y
echo "deb http://ubuntu-cloud.archive.canonical.com/ubuntu" "trusty-updates/kilo main" > /etc/apt/sources.list.d/cloudarchive-kilo.list
echo
echo "--->> Updating Required Libraries..."
echo
sleep 1
apt-get update && apt-get dist-upgrade -y
echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mNeutron\033[0m" ;echo " ############################"
echo
sleep 1
echo "--->> Preparing System Controls..."
sleep 1
echo
echo "net.ipv4.ip_forward=1
net.ipv4.conf.all.rp_filter=0
net.ipv4.conf.default.rp_filter=0
" >> /etc/sysctl.conf
sysctl -p
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install neutron-plugin-ml2 neutron-plugin-openvswitch-agent neutron-l3-agent neutron-dhcp-agent neutron-metadata-agent -y
apt-get install neutron-vpn-agent neutron-lbaas-agent -y
echo
echo -en " Configuring Neutron Engine..."
sleep 1
echo "[DEFAULT]
verbose = True
core_plugin = ml2
service_plugins = firewall,vpnaas,router,lbaas
#service_plugins = router
allow_overlapping_ips = True

rpc_backend = rabbit
auth_strategy = keystone

state_path = /var/lib/neutron
lock_path = $state_path/lock
notification_driver = neutron.openstack.common.notifier.rpc_notifier

[quotas]

[database]

[agent]
root_helper = sudo /usr/bin/neutron-rootwrap /etc/neutron/rootwrap.conf

[keystone_authtoken]
auth_uri = http://Cirrus-Controller:5000
auth_url = http://Cirrus-Controller:35357
auth_plugin = password
project_domain_id = default
user_domain_id = default
project_name = service
username = neutron
password = n00rinfra

[service_providers]
service_provider=LOADBALANCER:Haproxy:neutron.services.loadbalancer.drivers.haproxy.plugin_driver.HaproxyOnHostPluginDriver:default
service_provider=VPN:openswan:neutron.services.vpn.service_drivers.ipsec.IPsecVPNDriver:default

[oslo_messaging_rabbit]
rabbit_host = Cirrus-Controller
rabbit_userid = openstack
rabbit_password = n00rinfra
" > /etc/neutron/neutron.conf
echo "[OK]"
echo
echo -en " Configuring Neutron L2 Plugin..."
sleep 1
echo "[ml2]
type_drivers = flat,vlan,gre,vxlan
tenant_network_types = gre
mechanism_drivers = openvswitch

[ml2_type_flat]
flat_networks = external

[ml2_type_vlan]

[ml2_type_gre]
tunnel_id_ranges = 1:1000

[ml2_type_vxlan]

[securitygroup]
enable_security_group = True
enable_ipset = True
firewall_driver = neutron.agent.linux.iptables_firewall.OVSHybridIptablesFirewallDriver

[ovs]
local_ip = $MYIP1
bridge_mappings = external:br-ex

[agent]
tunnel_types = gre
" > /etc/neutron/plugins/ml2/ml2_conf.ini
echo "[OK]"
echo
echo -en " Configuring Neutron L3 Plugin..."
sleep 1
echo "[DEFAULT]
verbose = True
interface_driver = neutron.agent.linux.interface.OVSInterfaceDriver
external_network_bridge =
router_delete_namespaces = True
" > /etc/neutron/l3_agent.ini
echo "[OK]"
echo
echo -en " Configuring Neutron DHCP Plugin..."
sleep 1
echo "[DEFAULT]
verbose = True
interface_driver = neutron.agent.linux.interface.OVSInterfaceDriver
dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
dhcp_delete_namespaces = True
dnsmasq_config_file = /etc/neutron/dnsmasq-neutron.conf
" > /etc/neutron/dhcp_agent.ini
echo "[OK]"
echo
echo -en " Configuring Neutron MTU Plugin..."
sleep 1
echo "dhcp-option-force=26,1454
" > /etc/neutron/dnsmasq-neutron.conf
echo "[OK]"
pkill dnsmasq
echo
echo -en " Configuring Neutron MetaData Plugin..."
sleep 1
echo "[DEFAULT]
verbose = True

auth_uri = http://Cirrus-Controller:5000
auth_url = http://Cirrus-Controller:35357
auth_region = RegionOne
auth_plugin = password
project_domain_id = default
user_domain_id = default
project_name = service
username = neutron
password = n00rinfra

nova_metadata_ip = Cirrus-Controller

metadata_proxy_shared_secret = n00rinfra
" > /etc/neutron/metadata_agent.ini
echo "[OK]"
echo
echo -en " Configuring Neutron FireWall as a Service Plugin..."
sleep 1
echo "[fwaas]
driver = neutron_fwaas.services.firewall.drivers.linux.iptables_fwaas.IptablesFwaasDriver
enabled = True
" > /etc/neutron/fwaas_driver.ini
echo "[OK]"
echo
echo -en " Configuring Neutron LoadBalancer as a Service Plugin..."
sleep 1
echo "[DEFAULT]
interface_driver = neutron.agent.linux.interface.OVSInterfaceDriver
device_driver = neutron.services.loadbalancer.drivers.haproxy.namespace_driver.HaproxyNSDriver

[haproxy]
" > /etc/neutron/lbaas_agent.ini 
echo "[OK]"
echo
echo -en "--->> Configuring Open Vswitch..."
sleep 1
echo "[OK]"
echo
service openvswitch-switch restart
echo
echo -en "--->> Adding External Bridge..."
sleep 1
ovs-vsctl add-br br-ex
echo "[OK]"
echo
echo -en "--->> Mapping Bridge to Interface..."
sleep 1
ovs-vsctl add-port br-ex $port2
echo "[OK]"
echo
echo -en "--->> Disabling Generic Receive Offload (GRO)..."
sleep 1
ethtool -K $port2 gro off
echo "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
@reboot /sbin/ethtool -K $port2 gro off
" > /etc/cron.d/gro-off
echo "[OK]"
echo
echo "--->> Finalizing Modules Installation..."
echo
sleep 1
mkdir /lock 
chmod 777 /lock
service neutron-plugin-openvswitch-agent restart
service neutron-l3-agent restart
service neutron-dhcp-agent restart
service neutron-metadata-agent restart
service neutron-ovs-cleanup restart
service neutron-lbaas-agent restart
service neutron-vpn-agent restart
service openvswitch-switch restart
echo
echo
echo -en " #################################### " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack IS READY ! ############################"
sleep 1
echo
echo " --> Before u begin using you have to do the following:"
echo " 1- Edit and adjust /etc/hosts based on your nodes."
echo " 2- Create Tenant & External Networks on Cirrus-Controller ($controllerip)"
echo " 3- Enable Promsic Mode on External Iface"
echo
echo
echo
echo "                             !! The system will now reboot !! "
echo "                                  Hit Enter to Continue        "
read
echo
echo
reboot
echo
}}}

= Post Installation =

== Hosts Recognition ==

- After the system boots, you need to Configure the "/etc/hosts" file.

- Typical Hosts File for SDN Node is as follows.

{{{
127.0.0.1       localhost.localdomain localhost
192.168.0.131   Cirrus-SDN.noor.net Cirrus-SDN

## Cirrus-OpenStack Nodes
# MANG
192.168.0.130     Cirrus-Controller.noor.net Cirrus-Controller
192.168.0.131     Cirrus-SDN.noor.net Cirrus-SDN

192.168.0.124     Cirrus-Compute1.noor.net Cirrus-Compute1
192.168.0.125     Cirrus-Compute2.noor.net Cirrus-Compute2
192.168.0.126     Cirrus-Compute3.noor.net Cirrus-Compute3

192.168.0.124     Cirrus-Block1.noor.net Cirrus-Block1
192.168.0.125     Cirrus-Block2.noor.net Cirrus-Block2
192.168.0.126     Cirrus-Block3.noor.net Cirrus-Block3

# TUNNEL
10.0.1.1          Cirrus-SDN.noor.net Cirrus-SDN
10.0.1.2          Cirrus-Compute1.noor.net Cirrus-Compute1
10.0.1.3          Cirrus-Compute2.noor.net Cirrus-Compute2
10.0.1.4          Cirrus-Compute3.noor.net Cirrus-Compute3

# STORAGE
10.0.2.1          Cirrus-Compute1.noor.net Cirrus-Compute1
10.0.2.2          Cirrus-Compute2.noor.net Cirrus-Compute2
10.0.2.3          Cirrus-Compute3.noor.net Cirrus-Compute3
}}}

= Running Services =

- For List purposes, below are the Services running on Cirrus-SDN Node

{{{
service openvswitch-switch restart
service neutron-plugin-openvswitch-agent restart
service neutron-l3-agent restart
service neutron-dhcp-agent restart
service neutron-metadata-agent restart
service neutron-ovs-cleanup restart
service neutron-lbaas-agent restart
service neutron-vpn-agent restart
service openvswitch-switch restart
}}}
