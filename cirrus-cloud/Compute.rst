{{{
Author       : Mohamed Ashraf
Title        : CIRRUS-COMPUTE Node Deployment
Creation date: 18/10/2015
Latest update: 18/10/2015
}}}

{{attachment:logo-c2.jpg}}

<<TableOfContents(4)>>

= Preparing System =

- First off, you need to apply the following to prepare the system for Cirrus-Compute.

== Updating System ==

- Fully update the system Packages and kernel before you begin doing anything.

- Apply the following command to update all

{{{
apt-get update && apt-get upgrade -y && apt-get dist-upgrade -y && apt-get --purge autoremove -y && apt-get autoclean
}}}


== Network Configuration ==

- Configure Network interfaces of the Node, by setting Static IPs to each required Interface.

- Typical Network Configuration for Compute Node is as below

{{{
# The loopback network interface
auto lo
iface lo inet loopback

## MANGMENT NETWORK
auto eth0.1
iface eth0.1 inet static
        address 192.168.0.124
        netmask 255.255.255.0
        gateway 192.168.0.1
        dns-nameservers 192.168.0.4
        dns-search noor.net
        vlan-raw-device eth0

## TUNNEL NETWORK
auto eth0.2000
iface eth0.2000 inet static
        address 10.0.1.2
        netmask 255.255.255.0
        vlan-raw-device eth0

## STORAGE NETWORK
auto eth0.2001
iface eth0.2001 inet static
        address 10.0.2.1
        netmask 255.255.255.0
        vlan-raw-device eth0
}}}

- After finishing Above two Steps, you need to '''REBOOT'''.





= Deployment Script =

- Copy & Paste Below Script in the system.

- Replace the Ports that u need to Configure in the "CONFIGURE USED PORTS" inside the script  ex :{port0=eth0, port0=eth1,...}

- Configure the IP Address of the controller node in the "CONFIGURE NODES" Section, and the current compute node number.
  
    '''''EX:''''' if this is the first compute node then name it "Cirrus-Compute1" as below or "Cirrus-Compute2" for the 2nd node and so on.

- Run as root to Deploy Cirrus-Compute Node.

- The script will confirm the used Network Interfaces before it begins.

{{{
! /bin/bash         <- PASRER, PRECEDE WITH "#"
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games

##################### SCRIPT CONFIGURATION
#CONFIGURE NODES:
###
nodec=Cirrus-Compute1
nodeb=Cirrus-Block1
controllerip=192.168.0.130
###
#CONFIGURE USED PORTS:
###
port0=eth0            #Mangment Network
port1=eth1            #Tunnel Network
port2=eth2            #Storage Network
####################################################

MYIP0=$(ifconfig $port0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
MYIP1=$(ifconfig $port1 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
MYIP2=$(ifconfig $port2 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo -en "                                         " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack Compute Auto Deployment   "
echo "                                                  Script by Mohamed Ashraf "
echo
echo
echo " -> Please run this script as root! "
echo
echo " Parameters INFO:"
echo "-----------------"
echo " - Mangment Network IP will be used from $port0 : $MYIP0"
echo " - Tunnel Network IP will be used from $port1 : $MYIP1"
echo " - Storage Network IP will be used from $port2 : $MYIP2"
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
apt-get install ssh htop pydf unzip iftop snmpd snmp make nano tcpdump gcc sudo dnsutils ntp -y
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
echo $nodec > /etc/hostname
sleep 1
echo "[OK]"
echo
echo -en " Configuring HOSTS File....."
echo "
127.0.0.1       localhost.localdomain localhost
$MYIP0           $nodec.noor.net $nodec
$MYIP0           $nodeb.noor.net $nodeb

#Cirrus-OpenStack Nodes
$controllerip     Cirrus-Controller.noor.net Cirrus-Controller
$MYIP0            $nodec.noor.net $nodec
$MYIP0            $nodeb.noor.net $nodeb

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
echo -en " #################################### Preparing " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack Compute Environment ############################"
echo
echo -en " Configuring NTP Server....."
sleep 1
sed -i 's/server /#server /g' /etc/ntp.conf
echo "server Cirrus-Controller" >> /etc/ntp.conf
echo "[OK]"
echo
echo -en " Adjusting Hostname..."
sleep 1
hostname $nodec
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
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mNova\033[0m" ;echo " ############################"
echo
sleep 1
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install nova-compute sysfsutils -y
echo
echo -en " Configuring Nova..."
sleep 1
echo "[DEFAULT]
reserved_host_memory_mb = 0
ram_allocation_ratio = 1.5
cpu_allocation_ratio = 16.0
disk_allocation_ratio = 1.0

verbose = True
rpc_backend = rabbit
auth_strategy = keystone

my_ip = 192.168.0.124
vnc_enabled = True
vncserver_listen = 0.0.0.0
vncserver_proxyclient_address = 192.168.0.124
novncproxy_base_url = http://Cirrus-Controller:6080/vnc_auto.html

network_api_class = nova.network.neutronv2.api.API
security_group_api = neutron
linuxnet_interface_driver = nova.network.linux_net.LinuxOVSInterfaceDriver
firewall_driver = nova.virt.firewall.NoopFirewallDriver

dhcpbridge_flagfile=/etc/nova/nova.conf
dhcpbridge=/usr/bin/nova-dhcpbridge
logdir=/var/log/nova
state_path=/var/lib/nova
lock_path=/var/lock/nova
force_dhcp_release=True
iscsi_helper=tgtadm
libvirt_use_virtio_for_bridges=True
connection_type=libvirt
root_helper=sudo nova-rootwrap /etc/nova/rootwrap.conf
verbose=True
ec2_private_dns_show_ip=True
api_paste_config=/etc/nova/api-paste.ini
volumes_path=/var/lib/nova/volumes
enabled_apis=ec2,osapi_compute,metadata

instance_usage_audit = True
instance_usage_audit_period = hour
notify_on_state_change = vm_and_task_state
notification_driver = messagingv2

allow_resize_to_same_host=true
allow_migrate_to_same_host=true

[oslo_messaging_rabbit]
rabbit_host = Cirrus-Controller
rabbit_userid = openstack
rabbit_password = n00rinfra

[keystone_authtoken]
auth_uri = http://Cirrus-Controller:5000
auth_url = http://Cirrus-Controller:35357
auth_plugin = password
project_domain_id = default
user_domain_id = default
project_name = service
username = nova
password = n00rinfra

[glance]
host = Cirrus-Controller

[oslo_concurrency]
lock_path = /var/lib/nova/tmp

[neutron]
url = http://Cirrus-Controller:9696
auth_strategy = keystone
admin_auth_url = http://Cirrus-Controller:35357/v2.0
admin_tenant_name = service
admin_username = neutron
admin_password = n00rinfra
" > /etc/nova/nova.conf
echo "[OK]"
echo
echo -en " Configuring Virtualization Driver..."
sleep 1
testvir=$(egrep -c '(vmx|svm)' /proc/cpuinfo)
if [ $testvir == 0 ]
then
echo "[DEFAULT]
compute_driver=libvirt.LibvirtDriver
[libvirt]
virt_type=qemu
" > /etc/nova/nova-compute.conf
else
echo "[DEFAULT]
compute_driver=libvirt.LibvirtDriver
[libvirt]
virt_type=kvm
" > /etc/nova/nova-compute.conf
fi
echo "[OK]"
echo
service nova-compute restart
rm -f /var/lib/nova/nova.sqlite

echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mNeutron\033[0m" ;echo " ############################"
echo
sleep 1
echo "--->> Preparing System Controls..."
sleep 1
echo
echo "net.ipv4.conf.all.rp_filter=0
net.ipv4.conf.default.rp_filter=0
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
" >> /etc/sysctl.conf
sysctl -p
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install neutron-plugin-ml2 neutron-plugin-openvswitch-agent -y
echo
echo -en " Configuring Neutron Engine..."
sleep 1
echo "[DEFAULT]
verbose = True

core_plugin = ml2
#service_plugins = router,lbaas,metering,firewall,vpnaas
service_plugins = router
allow_overlapping_ips = True

rpc_backend = rabbit
auth_strategy = keystone

state_path = /var/lib/neutron
lock_path = $state_path/lock
notification_driver = neutron.openstack.common.notifier.rpc_notifier

[quotas]

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

[agent]
tunnel_types = gre
" > /etc/neutron/plugins/ml2/ml2_conf.ini
echo "[OK]"
#echo
#echo " Configuring Neutron FireWall as a Service Plugin..."
#sleep 1
#echo "[fwaas]
#driver = neutron_fwaas.services.firewall.drivers.linux.iptables_fwaas.IptablesFwaasDriver
#enabled = True
#" > /etc/neutron/fwaas_driver.ini
#echo "[OK]"
echo
mkdir /lock 
chmod 777 /lock
service openvswitch-switch restart
service nova-compute restart
service neutron-plugin-openvswitch-agent restart
service neutron-ovs-cleanup restart
echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mCinder\033[0m" ;echo " ############################"
echo
sleep 1
echo "--->> Installing LVM Packages..."
echo
sleep 1
apt-get install qemu lvm2 -y
echo
echo -en " Configuring LVM..."
sleep 1
echo 'devices {
    dir = "/dev"
    scan = [ "/dev" ]
    obtain_device_list_from_udev = 1
    preferred_names = [ ]
    filter = [ "a/sdb/", "r/.*/"]
    cache_dir = "/run/lvm"
    cache_file_prefix = ""
    write_cache_state = 1
    sysfs_scan = 1
    multipath_component_detection = 1
    md_component_detection = 1
    md_chunk_alignment = 1
    data_alignment_detection = 1
    data_alignment = 0
    data_alignment_offset_detection = 1
    ignore_suspended_devices = 0
    disable_after_error_count = 0
    require_restorefile_with_uuid = 1
    pv_min_size = 2048
    issue_discards = 1
}
allocation {
 
    maximise_cling = 1
    mirror_logs_require_separate_pvs = 0
    thin_pool_metadata_require_separate_pvs = 0
}
log {
    verbose = 0
    silent = 0
    syslog = 1
    overwrite = 0
    level = 0
    indent = 1
    command_names = 0
    prefix = "  "
}
backup {
    backup = 1
    backup_dir = "/etc/lvm/backup"
    archive = 1
    archive_dir = "/etc/lvm/archive"
    retain_min = 10
    retain_days = 30
}
shell {
    history_size = 100
}
global {
    umask = 077
    test = 0
    units = "h"
    si_unit_consistency = 1
    activation = 1
    proc = "/proc"
    locking_type = 1
    wait_for_locks = 1
    fallback_to_clustered_locking = 1
    fallback_to_local_locking = 1
    locking_dir = "/run/lock/lvm"
    prioritise_write_locks = 1
    abort_on_internal_errors = 0
    detect_internal_vg_cache_corruption = 0
    metadata_read_only = 0
    mirror_segtype_default = "mirror"
    use_lvmetad = 0
    thin_check_executable = "/usr/sbin/thin_check"
    thin_check_options = [ "-q" ]
}
activation {
    checks = 0
    udev_sync = 1
    udev_rules = 1
    verify_udev_operations = 0
    retry_deactivation = 1
    missing_stripe_filler = "error"
    use_linear_target = 1
    reserved_stack = 64
    reserved_memory = 8192
    process_priority = -18
    mirror_region_size = 512
    readahead = "auto"
    raid_fault_policy = "warn"
    mirror_log_fault_policy = "allocate"
    mirror_image_fault_policy = "remove"
    snapshot_autoextend_threshold = 100
    snapshot_autoextend_percent = 20
    thin_pool_autoextend_threshold = 100
    thin_pool_autoextend_percent = 20
    use_mlockall = 0
    monitoring = 0
    polling_interval = 15
}
dmeventd {
    mirror_library = "libdevmapper-event-lvm2mirror.so"
    snapshot_library = "libdevmapper-event-lvm2snapshot.so"
    thin_library = "libdevmapper-event-lvm2thin.so"
}
' > /etc/lvm/lvm.conf
echo "[OK]"
echo
pvcreate /dev/sdb1
vgcreate cinder-volumes /dev/sdb1
echo
sleep 1
echo "--->> Installing Cinder Packages..."
echo
sleep 1
apt-get install cinder-volume python-mysqldb -y
echo
echo -en " Configuring Cinder..."
sleep 1
echo "[DEFAULT]
verbose = True
my_ip = $MYIP0
enabled_backends = lvm

rpc_backend = rabbit
auth_strategy = keystone

control_exchange = cinder
notification_driver = messagingv2

rootwrap_config = /etc/cinder/rootwrap.conf
api_paste_confg = /etc/cinder/api-paste.ini
iscsi_helper = tgtadm
volume_name_template = volume-%s
volume_group = cinder-volumes
verbose = True
auth_strategy = keystone
state_path = /var/lib/cinder
lock_path = /var/lock/cinder
volumes_dir = /var/lib/cinder/volumes

glance_host = Cirrus-Controller

storage_availability_zone=CIRRUS

[database]
connection = mysql://cinder:n00rinfra@Cirrus-Controller/cinder

[oslo_messaging_rabbit]
rabbit_host = Cirrus-Controller
rabbit_userid = openstack
rabbit_password = n00rinfra

[keystone_authtoken]
auth_uri = http://Cirrus-Controller:5000
auth_url = http://Cirrus-Controller:35357
auth_plugin = password
project_domain_id = default
user_domain_id = default
project_name = service
username = cinder
password = n00rinfra

[lvm]
volume_driver = cinder.volume.drivers.lvm.LVMVolumeDriver
volume_group = cinder-volumes
iscsi_protocol = iscsi
iscsi_helper = tgtadm
volume_clear_size=50
volume_clear=none
type=thin

[oslo_concurrency]
lock_path = /var/lock/cinder
" > /etc/cinder/cinder.conf
echo "[OK]"
echo
service tgt restart
service cinder-volume restart
rm -f /var/lib/cinder/cinder.sqlite
echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mCeilometer\033[0m" ;echo " ############################"
sleep1
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install ceilometer-agent-compute -y
echo
echo -en " Configuring Ceilometer..."
sleep 1
echo "[DEFAULT]
rpc_backend = rabbit
verbose = True

[database]

[keystone_authtoken]
auth_uri = http://Cirrus-Controller:5000/v2.0
identity_uri = http://Cirrus-Controller:35357
admin_tenant_name = service
admin_user = ceilometer
admin_password = n00rinfra      

[matchmaker_redis]

[matchmaker_ring]

[oslo_messaging_amqp]

[oslo_messaging_qpid]

[oslo_messaging_rabbit]
rabbit_host = Cirrus-Controller
rabbit_userid = openstack
rabbit_password = n00rinfra  

[publisher]
telemetry_secret = n00rinfra       

[service_credentials]
os_auth_url = http://Cirrus-Controller:5000/v2.0
os_username = ceilometer
os_tenant_name = service
os_password = n00rinfra      
os_endpoint_type = internalURL
os_region_name = RegionOne
" > /etc/ceilometer/ceilometer.conf
echo "[OK]"
echo
echo "--->> Finalizing Ceilometer Installation..."
echo
sleep 1
service ceilometer-agent-compute restart
service nova-compute restart
service cinder-volume restart
echo
echo
echo -en " #################################### " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack IS READY ! ############################"
sleep 1
echo
echo " --> Before u begin using you have to do the following:"
echo " 1- Edit and adjust /etc/hosts based on your nodes."
echo " 2- Create Tenant & External Networks on Cirrus-Controller ($controllerip)"
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

- Typical Hosts File for Compute Node is as follows.

{{{
127.0.0.1       localhost.localdomain localhost
192.168.0.124   Cirrus-Compute1.noor.net Cirrus-Compute1
192.168.0.124   Cirrus-Block1.noor.net Cirrus-Block1

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

- For List purposes, below are the Services running on Cirrus-Compute Node

{{{
service nova-compute restart
service openvswitch-switch restart
service nova-compute restart
service neutron-plugin-openvswitch-agent restart
service neutron-ovs-cleanup restart
service tgt restart
service cinder-volume restart
service ceilometer-agent-compute restart
}}}
