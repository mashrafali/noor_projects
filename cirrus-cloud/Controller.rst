{{{
Author       : Mohamed Ashraf
Title        : CIRRUS-CONTROLLER Node Deployment
Creation date: 18/10/2015
Latest update: 18/10/2015
}}}


{{attachment:logo-c1.jpg}}

<<TableOfContents(4)>>


= Preparing System =

- First off, you need to apply the following to prepare the system for Cirrus-Controller.

== Updating System ==

- Fully update the system Packages and kernel before you begin doing anything.

- Apply the following command to update all

{{{
apt-get update && apt-get upgrade -y && apt-get dist-upgrade -y && apt-get --purge autoremove -y && apt-get autoclean
}}}

== Network Configuration ==

- Configure Network interfaces of the Node, by setting Static IPs to each required Interface.

- Typical Network Configuration for Controller Node is as below

{{{
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto p5p1
iface p5p1 inet static
        address 192.168.0.130
        netmask 255.255.255.0
        network 192.168.0.0
        broadcast 192.168.0.255
        gateway 192.168.0.1
        dns-nameservers 192.168.0.4
        dns-search noor.net
}}}

- After finishing Above two Steps, you need to '''REBOOT'''.






= Deployment Script =

- Copy & Paste Below Script in the system.

- Replace the Ports that u need to Configure in the "CONFIGURE USED PORTS" inside the script  ex :{port0=eth0, port0=eth1,...}

- Run as root to Deploy Cirrus-Controller Node.

- The script will confirm the used Network Interfaces before it begins.

{{{
! /bin/bash         <- PASRER, PRECEDE WITH "#"

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games

##################### SCRIPT CONFIGURATION
#
#CONFIGURE USED PORTS:
###
port0=eth0
####################################################

MYIP=$(ifconfig $port0 | grep -i "inet addr:" | awk -F '[/:]' '{print $2}' | awk -F '[/ ]' '{print $1}')
clear
echo
echo -en "                                         " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack Controller Auto Deployment   "
echo "                                                  Script by Mohamed Ashraf "
echo
echo
echo " -> Please run this script as root! "
echo
echo " Parameters INFO:"
echo "-----------------"
echo " - Mangment Network IP will be used from $port0 : $MYIP"
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
echo Cirrus-Controller > /etc/hostname
sleep 1
echo "[OK]"
echo
echo -en " Configuring HOSTS File....."
echo "
127.0.0.1       localhost.localdomain localhost
$MYIP           controller.noor.net controller

#Cirrus-OpenStack Nodes
$MYIP           Cirrus-Controller.noor.net Cirrus-Controller

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
echo -en " #################################### Preparing " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack Controller Environment ############################"
echo
#sed -i 's/server /#server /g' /etc/ntp.conf
echo -en " Configuring NTP Server....."
sleep 1
sed -i 's#restrict -4 default kod notrap nomodify nopeer noquery#restrict -4 default kod notrap nomodify#g' /etc/ntp.conf 
sed -i 's#restrict -6 default kod notrap nomodify nopeer noquery#restrict -6 default kod notrap nomodify#g' /etc/ntp.conf
echo "[OK]"
echo
echo -en " Adjusting Hostname..."
sleep 1
hostname Cirrus-Controller
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
echo "--->> Installing DataBase..."
echo
sleep 1
debconf-set-selections <<< 'mysql-server mysql-server/root_password password n00rinfra'
debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password n00rinfra'
apt-get install mariadb-server python-mysqldb -y
echo
echo -en " Configuring DataBase..."
sleep 2
echo "[mysqld]
bind-address = $MYIP
default-storage-engine = innodb
innodb_file_per_table
collation-server = utf8_general_ci
init-connect = 'SET NAMES utf8'
character-set-server = utf8
" > /etc/mysql/conf.d/mysqld_openstack.cnf
echo "[OK]"
echo
service mysql restart
echo
echo "--->> Installing Rabbit Message queue..."
echo
sleep 1
apt-get install rabbitmq-server -y
echo
echo "--->> Configuring Rabbit Message queue..."
echo
sleep 2
rabbitmqctl add_user openstack n00rinfra
rabbitmqctl set_permissions openstack ".*" ".*" ".*"
echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mKeyStone\033[0m" ;echo " ############################"
echo
echo -en " Preparing Database..."
sleep 1
mysql -u root -pn00rinfra -Bse "CREATE DATABASE keystone;"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'n00rinfra';"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'n00rinfra';"
echo "[OK]"
echo
echo -en " Generating Admin Token..."
#token=$(openssl rand -hex 10)
token=n00rinfra
sleep 1
echo "[OK]"
echo
echo -en " Disable Keystone AutoStart..."
sleep 1
echo "manual" > /etc/init/keystone.override
echo "[OK]"
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install keystone python-openstackclient apache2 libapache2-mod-wsgi memcached python-memcache -y
echo
echo -en " Configuring Keystone..."
sleep 1
echo "[DEFAULT]
verbose = True
admin_token = n00rinfra
log_dir = /var/log/keystone

[assignment]

[auth]

[cache]

[catalog]

[credential]

[database]
connection = mysql://keystone:n00rinfra@Cirrus-Controller/keystone

[domain_config]

[endpoint_filter]

[endpoint_policy]

[eventlet_server]

[eventlet_server_ssl]

[federation]

[fernet_tokens]

[identity]

[identity_mapping]

[kvs]

[ldap]

[matchmaker_redis]

[matchmaker_ring]

[memcache]
servers = localhost:11211

[oauth1]

[os_inherit]

[oslo_messaging_amqp]

[oslo_messaging_qpid]

[oslo_messaging_rabbit]

[oslo_middleware]

[oslo_policy]

[paste_deploy]

[policy]

[resource]

[revoke]
driver = keystone.contrib.revoke.backends.sql.Revoke

[role]

[saml]

[signing]

[ssl]

[token]
provider = keystone.token.providers.uuid.Provider
driver = keystone.token.persistence.backends.memcache.Token

[trust]

[extra_headers]
Distribution = Ubuntu
" > /etc/keystone/keystone.conf

echo "[OK]"
echo
echo "--->> Populating Database..."
echo
sleep 1
su -s /bin/sh -c "keystone-manage db_sync" keystone
echo
echo -en " Configuring Apache..."
sleep 1
echo "ServerName Cirrus-Controller" >> /etc/apache2/apache2.conf
echo 'Listen 5000
Listen 35357

<VirtualHost *:5000>
    WSGIDaemonProcess keystone-public processes=5 threads=1 user=keystone display-name=%{GROUP}
    WSGIProcessGroup keystone-public
    WSGIScriptAlias / /var/www/cgi-bin/keystone/main
    WSGIApplicationGroup %{GLOBAL}
    WSGIPassAuthorization On
    <IfVersion >= 2.4>
      ErrorLogFormat "%{cu}t %M"
    </IfVersion>
    LogLevel info
    ErrorLog /var/log/apache2/keystone-error.log
    CustomLog /var/log/apache2/keystone-access.log combined
</VirtualHost>

<VirtualHost *:35357>
    WSGIDaemonProcess keystone-admin processes=5 threads=1 user=keystone display-name=%{GROUP}
    WSGIProcessGroup keystone-admin
    WSGIScriptAlias / /var/www/cgi-bin/keystone/admin
    WSGIApplicationGroup %{GLOBAL}
    WSGIPassAuthorization On
    <IfVersion >= 2.4>
      ErrorLogFormat "%{cu}t %M"
    </IfVersion>
    LogLevel info
    ErrorLog /var/log/apache2/keystone-error.log
    CustomLog /var/log/apache2/keystone-access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/wsgi-keystone.conf
echo "[OK]"
echo
echo -en " Enabling Virtual Hosts..."
ln -s /etc/apache2/sites-available/wsgi-keystone.conf /etc/apache2/sites-enabled
sleep 1
echo "[OK]"
echo
echo "--->> Configuring WSGI Components..."
echo
mkdir -p /var/www/cgi-bin/keystone
sleep 1
curl http://git.openstack.org/cgit/openstack/keystone/plain/httpd/keystone.py?h=stable/kilo | tee /var/www/cgi-bin/keystone/main /var/www/cgi-bin/keystone/admin
sleep 2
chown -R keystone:keystone /var/www/cgi-bin/keystone
chmod 755 /var/www/cgi-bin/keystone/*
echo
service apache2 restart
rm -f /var/lib/keystone/keystone.db
echo
echo "--->> CREATING SERVICE ENTITY AND ENDPOINTS:"
echo
sleep 1
export OS_TOKEN=n00rinfra
export OS_URL=http://Cirrus-Controller:35357/v2.0
openstack service create --name keystone --description "OpenStack Identity" identity
openstack endpoint create --publicurl http://Cirrus-Controller:5000/v2.0 --internalurl http://Cirrus-Controller:5000/v2.0 --adminurl http://Cirrus-Controller:35357/v2.0 --region RegionOne identity
echo
echo "--->> CREATING PROJECTS AND USERS:"
echo
sleep 1
openstack project create --description "Admin Project" admin
openstack user create --password n00rinfra admin
openstack role create admin
openstack role add --project admin --user admin admin
openstack project create --description "Service Project" service
openstack project create --description "Demo Project" demo
openstack user create --password demo demo
openstack role create user
openstack role add --project demo --user demo user
unset OS_TOKEN OS_URL
echo
echo -en " Creating environment scripts..."
sleep 1
echo "export OS_PROJECT_DOMAIN_ID=default
export OS_USER_DOMAIN_ID=default
export OS_PROJECT_NAME=admin
export OS_TENANT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=n00rinfra
export OS_AUTH_URL=http://Cirrus-Controller:35357/v3" > /etc/keystone/admin-openrc.sh
echo "export OS_PROJECT_DOMAIN_ID=default
export OS_USER_DOMAIN_ID=default
export OS_PROJECT_NAME=demo
export OS_TENANT_NAME=demo
export OS_USERNAME=demo
export OS_PASSWORD=demo
export OS_AUTH_URL=http://Cirrus-Controller:5000/v3" > /etc/keystone/demo-openrc.sh
echo "[OK]"
echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mGlance\033[0m" ;echo " ############################"
echo
echo -en " Preparing Database..."
sleep 1
mysql -u root -pn00rinfra -Bse "CREATE DATABASE glance;"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'n00rinfra';"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'n00rinfra';"
echo "[OK]"
echo
echo "--->> Configuring Service endpoints..."
echo
sleep 1
source /etc/keystone/admin-openrc.sh
openstack user create --password n00rinfra glance
openstack role add --project service --user glance admin
openstack service create --name glance --description "OpenStack Image service" image
openstack endpoint create --publicurl http://Cirrus-Controller:9292 --internalurl http://Cirrus-Controller:9292 --adminurl http://Cirrus-Controller:9292 --region RegionOne image
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install glance python-glanceclient -y
echo
echo -en " Configuring Glance..."
sleep 1
echo "[DEFAULT]
verbose = True

bind_host = 0.0.0.0
bind_port = 9292
log_file = /var/log/glance/api.log
backlog = 4096
registry_host = 0.0.0.0
registry_port = 9191
registry_client_protocol = http
rabbit_host = localhost
rabbit_port = 5672
rabbit_use_ssl = false
rabbit_userid = guest
rabbit_password = guest
rabbit_virtual_host = /
rabbit_notification_exchange = glance
rabbit_notification_topic = notifications
rabbit_durable_queues = False
qpid_notification_exchange = glance
qpid_notification_topic = notifications
qpid_hostname = localhost
qpid_port = 5672
qpid_username =
qpid_password =
qpid_sasl_mechanisms =
qpid_reconnect_timeout = 0
qpid_reconnect_limit = 0
qpid_reconnect_interval_min = 0
qpid_reconnect_interval_max = 0
qpid_reconnect_interval = 0
qpid_heartbeat = 5
qpid_protocol = tcp
qpid_tcp_nodelay = True
delayed_delete = False
scrub_time = 43200
scrubber_datadir = /var/lib/glance/scrubber
image_cache_dir = /var/lib/glance/image-cache/

notification_driver = messagingv2
rpc_backend = rabbit
rabbit_host = Cirrus-Controller
rabbit_userid = openstack
rabbit_password = n00rinfra 

[oslo_policy]

[database]
sqlite_db = /var/lib/glance/glance.sqlite
backend = sqlalchemy
connection = mysql://glance:n00rinfra@Cirrus-Controller/glance

[oslo_concurrency]

[keystone_authtoken]
auth_uri = http://Cirrus-Controller:5000
auth_url = http://Cirrus-Controller:35357
auth_plugin = password
project_domain_id = default
user_domain_id = default
project_name = service
username = glance
password = n00rinfra

[paste_deploy]
flavor = keystone

[store_type_location_strategy]

[profiler]

[task]

[taskflow_executor]

[glance_store]
default_store = file
filesystem_store_datadir = /var/lib/glance/images/
swift_store_auth_version = 2
swift_store_auth_address = 127.0.0.1:5000/v2.0/
swift_store_user = jdoe:jdoe
swift_store_key = a86850deb2742ec3cb41518e26aa2d89
swift_store_container = glance
swift_store_create_container_on_put = False
swift_store_large_object_size = 5120
swift_store_large_object_chunk_size = 200
s3_store_host = s3.amazonaws.com
s3_store_access_key = <20-char AWS access key>
s3_store_secret_key = <40-char AWS secret key>
s3_store_bucket = <lowercased 20-char aws access key>glance
s3_store_create_bucket_on_put = False
sheepdog_store_address = localhost
sheepdog_store_port = 7000
sheepdog_store_chunk_size = 64
" > /etc/glance/glance-api.conf
echo "[DEFAULT]
verbose = True

bind_host = 0.0.0.0
bind_port = 9191
log_file = /var/log/glance/registry.log
backlog = 4096
api_limit_max = 1000
limit_param_default = 25
rabbit_host = localhost
rabbit_port = 5672
rabbit_use_ssl = false
rabbit_userid = guest
rabbit_password = guest
rabbit_virtual_host = /
rabbit_notification_exchange = glance
rabbit_notification_topic = notifications
rabbit_durable_queues = False
qpid_notification_exchange = glance
qpid_notification_topic = notifications
qpid_hostname = localhost
qpid_port = 5672
qpid_username =
qpid_password =
qpid_sasl_mechanisms =
qpid_reconnect_timeout = 0
qpid_reconnect_limit = 0
qpid_reconnect_interval_min = 0
qpid_reconnect_interval_max = 0
qpid_reconnect_interval = 0
qpid_heartbeat = 5
qpid_protocol = tcp
qpid_tcp_nodelay = True

notification_driver = messagingv2
rpc_backend = rabbit
rabbit_host = Cirrus-Controller
rabbit_userid = openstack
rabbit_password = n00rinfra 

[oslo_policy]

[database]
sqlite_db = /var/lib/glance/glance.sqlite
backend = sqlalchemy
connection = mysql://glance:n00rinfra@Cirrus-Controller/glance

[keystone_authtoken]
auth_uri = http://Cirrus-Controller:5000
auth_url = http://Cirrus-Controller:35357
auth_plugin = password
project_domain_id = default
user_domain_id = default
project_name = service
username = glance
password = n00rinfra

[paste_deploy]
flavor = keystone

[profiler]
" > /etc/glance/glance-registry.conf
echo "[OK]"
echo
echo "--->> Populating Database..."
echo
sleep 1
su -s /bin/sh -c "glance-manage db_sync" glance
echo
service glance-registry restart
service glance-api restart
rm -f /var/lib/glance/glance.sqlite
echo "export OS_IMAGE_API_VERSION=2" | tee -a /etc/keystone/admin-openrc.sh /etc/keystone/demo-openrc.sh
source /etc/keystone/admin-openrc.sh
echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mNova\033[0m" ;echo " ############################"
echo
echo -en " Preparing Database..."
sleep 1
mysql -u root -pn00rinfra -Bse "CREATE DATABASE nova;"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' IDENTIFIED BY 'n00rinfra';"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' IDENTIFIED BY 'n00rinfra';"
echo "[OK]"
echo
echo "--->> Configuring Service endpoints..."
echo
sleep 1
openstack user create --password n00rinfra nova
openstack role add --project service --user nova admin
openstack service create --name nova --description "OpenStack Compute" compute
openstack endpoint create --publicurl http://Cirrus-Controller:8774/v2/%\(tenant_id\)s --internalurl http://Cirrus-Controller:8774/v2/%\(tenant_id\)s --adminurl http://Cirrus-Controller:8774/v2/%\(tenant_id\)s --region RegionOne compute
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install nova-api nova-cert nova-conductor nova-consoleauth nova-novncproxy nova-scheduler python-novaclient -y
echo
echo -en " Configuring Nova..."
sleep 1
echo "[DEFAULT]
my_ip = $MYIP
vncserver_listen = $MYIP
vncserver_proxyclient_address = $MYIP

verbose = True
rpc_backend = rabbit
auth_strategy = keystone

dhcpbridge_flagfile=/etc/nova/nova.conf
dhcpbridge=/usr/bin/nova-dhcpbridge
logdir=/var/log/nova
state_path=/var/lib/nova
lock_path=/var/lock/nova
force_dhcp_release=True
libvirt_use_virtio_for_bridges=True
verbose=True
ec2_private_dns_show_ip=True
api_paste_config=/etc/nova/api-paste.ini
enabled_apis=ec2,osapi_compute,metadata

network_api_class = nova.network.neutronv2.api.API
security_group_api = neutron
linuxnet_interface_driver = nova.network.linux_net.LinuxOVSInterfaceDriver
firewall_driver = nova.virt.firewall.NoopFirewallDriver

[database]
connection = mysql://nova:n00rinfra@Cirrus-Controller/nova

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
admin_auth_url = http://Cirrus-Controller:35357/v2.0
auth_strategy = keystone
admin_tenant_name = service
admin_username = neutron
admin_password = n00rinfra
service_metadata_proxy = True
metadata_proxy_shared_secret = n00rinfra
" > /etc/nova/nova.conf
echo "[OK]"
echo
echo "--->> Populating Database..."
echo
sleep 1
su -s /bin/sh -c "nova-manage db sync" nova
echo
service nova-api restart
service nova-cert restart
service nova-consoleauth restart
service nova-scheduler restart
service nova-conductor restart
service nova-novncproxy restart
rm -f /var/lib/nova/nova.sqlite
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mNeutron\033[0m" ;echo " ############################"
echo
echo -en " Preparing Database..."
sleep 1
mysql -u root -pn00rinfra -Bse "CREATE DATABASE neutron;"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' IDENTIFIED BY 'n00rinfra';"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' IDENTIFIED BY 'n00rinfra';"
echo "[OK]"
echo
echo "--->> Configuring Service endpoints..."
echo
sleep 1
openstack user create --password n00rinfra neutron
openstack role add --project service --user neutron admin
openstack service create --name neutron --description "OpenStack Networking" network
openstack endpoint create --publicurl http://Cirrus-Controller:9696 --adminurl http://Cirrus-Controller:9696 --internalurl http://Cirrus-Controllerr:9696 --region RegionOne network
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install neutron-server neutron-plugin-ml2 python-neutronclient -y
apt-get install python-neutron-vpnaas python-neutron-lbaas
#apt-get install neutron-lbaas-agent neutron-vpn-agent -y
echo
echo -en " Configuring Neutron Engine..."
sleep 1
echo "[DEFAULT]
verbose = True
core_plugin = ml2
service_plugins = firewall,vpnaas,router,lbaas
allow_overlapping_ips = True

notify_nova_on_port_status_changes = True
notify_nova_on_port_data_changes = True
nova_url = http://Cirrus-Controller:8774/v2

rpc_backend = rabbit
auth_strategy = keystone

[matchmaker_redis]

[matchmaker_ring]

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

[database]
connection = mysql://neutron:n00rinfra@Cirrus-Controller/neutron

[nova]
auth_url = http://Cirrus-Controller:35357
auth_plugin = password
project_domain_id = default
user_domain_id = default
region_name = RegionOne
project_name = service
username = nova
password = n00rinfra

[oslo_concurrency]
lock_path = $state_path/lock

[oslo_policy]

[oslo_messaging_amqp]

[oslo_messaging_qpid]

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
echo "--->> Populating Database..."
echo
sleep 1
su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron
echo
service nova-api restart
service neutron-server restart
#service neutron-lbaas-agent restart
#service neutron-metadata-agent restart
#service neutron-vpn-agent restart
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mHorizon\033[0m" ;echo " ############################"
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install openstack-dashboard -y
apt-get remove openstack-dashboard-ubuntu-theme -y
echo
echo -en " Configuring Horizon..."
sleep 1
sed -i 's/OPENSTACK_HOST = "127.0.0.1"/OPENSTACK_HOST = "Cirrus-Controller"/g' /etc/openstack-dashboard/local_settings.py
sed -i 's/OPENSTACK_KEYSTONE_DEFAULT_ROLE = "_member_"/OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"/g' /etc/openstack-dashboard/local_settings.py
sed -i 's/TIME_ZONE = "UTC"/TIME_ZONE = "EET"/g' /etc/openstack-dashboard/local_settings.py
echo "[OK]"
echo
echo -en " Configuring Apache..."
sleep 1
echo -en '<VirtualHost *:80>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        RedirectMatch ^/$ /horizon/
</VirtualHost>
' > /etc/apache2/sites-available/000-default.conf
echo "[OK]"
echo
echo "--->> Branding Cirrus OpenStack..."
echo
sleep 1
wget -P /tmp/images http://192.168.0.114/openstack-images/logo.png
wget -P /tmp/images http://192.168.0.114/openstack-images/logo-splash.png
mv /usr/share/openstack-dashboard/openstack_dashboard/static/dashboard/img/logo.png /usr/share/openstack-dashboard/openstack_dashboard/static/dashboard/img/logo.png.BAK
mv /usr/share/openstack-dashboard/openstack_dashboard/static/dashboard/img/logo-splash.png /usr/share/openstack-dashboard/openstack_dashboard/static/dashboard/img/logo-splash.png.BAK
mv /tmp/images/logo.png /usr/share/openstack-dashboard/openstack_dashboard/static/dashboard/img/logo.png
mv /tmp/images/logo-splash.png /usr/share/openstack-dashboard/openstack_dashboard/static/dashboard/img/logo-splash.png
chown horizon:horizon logo.png
chown horizon:horizon logo-splash.png
echo
service apache2 reload
service apache2 restart
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mCinder\033[0m" ;echo " ############################"
echo
echo -en " Preparing Database..."
sleep 1
mysql -u root -pn00rinfra -Bse "CREATE DATABASE cinder;"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'localhost' IDENTIFIED BY 'n00rinfra';"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'%' IDENTIFIED BY 'n00rinfra';"
echo "[OK]"
echo
echo "--->> Configuring Service endpoints..."
echo
sleep 1
openstack user create --password n00rinfra cinder
openstack role add --project service --user cinder admin
openstack service create --name cinder --description "OpenStack Block Storage" volume
openstack service create --name cinderv2 --description "OpenStack Block Storage" volumev2
openstack endpoint create --publicurl http://Cirrus-Controller:8776/v2/%\(tenant_id\)s --internalurl http://Cirrus-Controller:8776/v2/%\(tenant_id\)s --adminurl http://Cirrus-Controller:8776/v2/%\(tenant_id\)s --region RegionOne volume
openstack endpoint create --publicurl http://Cirrus-Controller:8776/v2/%\(tenant_id\)s --internalurl http://Cirrus-Controller:8776/v2/%\(tenant_id\)s --adminurl http://Cirrus-Controller:8776/v2/%\(tenant_id\)s --region RegionOne volumev2
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install cinder-api cinder-scheduler python-cinderclient -y
echo
echo -en " Configuring Cinder..."
sleep 1
echo "[DEFAULT]
verbose = True
my_ip = $MYIP
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

[oslo_concurrency]
lock_path = /var/lock/cinder
" > /etc/cinder/cinder.conf
echo "[OK]"
echo
echo "--->> Populating Database..."
echo
sleep 1
su -s /bin/sh -c "cinder-manage db sync" cinder
echo
service cinder-scheduler restart
service cinder-api restart
rm -f /var/lib/cinder/cinder.sqlite
echo
echo "export OS_VOLUME_API_VERSION=2" | tee -a /etc/keystone/admin-openrc.sh /etc/keystone/demo-openrc.sh
source /etc/keystone/admin-openrc.sh
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mHeat\033[0m" ;echo " ############################"
echo
echo -en " Preparing Database..."
sleep 1
mysql -u root -pn00rinfra -Bse "CREATE DATABASE heat;"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON heat.* TO 'heat'@'localhost' IDENTIFIED BY 'n00rinfra';"
mysql -u root -pn00rinfra -Bse "GRANT ALL PRIVILEGES ON heat.* TO 'heat'@'%' IDENTIFIED BY 'n00rinfra';"
echo "[OK]"
echo
echo "--->> Configuring Service endpoints..."
echo
sleep 1
openstack user create --password n00rinfra heat
openstack role add --project service --user heat admin
openstack role create heat_stack_owner
openstack role add --project demo --user demo heat_stack_owner
openstack role create heat_stack_user
openstack service create --name heat --description "Orchestration" orchestration
openstack service create --name heat-cfn --description "Orchestration" cloudformation
openstack endpoint create --publicurl http://Cirrus-Controller:8004/v1/%\(tenant_id\)s --internalurl http://Cirrus-Controller:8004/v1/%\(tenant_id\)s --adminurl http://Cirrus-Controller:8004/v1/%\(tenant_id\)s --region RegionOne orchestration
openstack endpoint create --publicurl http://Cirrus-Controller:8000/v1 --internalurl http://Cirrus-Controller:8000/v1 --adminurl http://Cirrus-Controller:8000/v1 --region RegionOne cloudformation
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install heat-api heat-api-cfn heat-engine python-heatclient -y
echo
echo -en " Configuring Heat..."
sleep 1
echo "[DEFAULT]
verbose = True
rpc_backend = rabbit
heat_metadata_server_url = http://Cirrus-Controller:8000
heat_waitcondition_server_url = http://Cirrus-Controller:8000/v1/waitcondition

stack_domain_admin = heat_domain_admin
stack_domain_admin_password = n00rinfra       
stack_user_domain_name = heat_user_domain

[database]
connection = mysql://heat:n00rinfra@Cirrus-Controller/heat  

[keystone_authtoken]
auth_uri = http://Cirrus-Controller:5000/v2.0
identity_uri = http://Cirrus-Controller:35357
admin_tenant_name = service
admin_user = heat
admin_password = n00rinfra

[matchmaker_redis]

[matchmaker_ring]

[oslo_messaging_amqp]

[oslo_messaging_qpid]

[oslo_messaging_rabbit]
rabbit_host = Cirrus-Controller
rabbit_userid = openstack
rabbit_password = n00rinfra  

[ec2authtoken]
auth_uri = http://Cirrus-Controller:5000/v2.0
" > /etc/heat/heat.conf
echo "[OK]"
echo
echo "--->> Creating the Heat Domain..."
echo
sleep 1
heat-keystone-setup-domain --stack-user-domain-name heat_user_domain --stack-domain-admin heat_domain_admin --stack-domain-admin-password n00rinfra | grep "stack_user_domain_id=" > /tmp/heat-id
heatid=$(cat /tmp/heat-id)
sed -e"/^stack_user_domain_name = heat_user_domain/a $heatid" /etc/heat/heat.conf > /tmp/heat.conf
mv /tmp/heat.conf /etc/heat/heat.conf
rm /tmp/heat-id
echo
echo "--->> Populating Database..."
echo
sleep 1
su -s /bin/sh -c "heat-manage db_sync" heat
echo
service heat-api restart
service heat-api-cfn restart
service heat-engine restart
rm -f /var/lib/heat/heat.sqlite
echo
echo
echo -en " #################################### Installing & Configuring "; echo  -en "\033[31;1mCeilometer\033[0m" ;echo " ############################"
echo
echo "--->> Installing Mongo DataBase..."
echo
sleep 1
apt-get install mongodb-server mongodb-clients python-pymongo -y
echo
echo -en " Preparing Database..."
sleep 1
echo "dbpath=/var/lib/mongodb
logpath=/var/log/mongodb/mongodb.log
logappend=true
bind_ip = $MYIP0
journal=true
smallfiles = true
" > /etc/mongodb.conf
echo "[OK]"
echo
service mongodb stop
rm /var/lib/mongodb/journal/prealloc.*
service mongodb start
service mongodb restart
echo "--->> Installing Ceilometer DataBase..."
echo
sleep 1
mongo --host Cirrus-Controller --eval '
  db = db.getSiblingDB("ceilometer");
  db.addUser({user: "ceilometer",
  pwd: "n00rinfra",
  roles: [ "readWrite", "dbAdmin" ]})'
echo
echo "--->> Configuring Service endpoints..."
echo
sleep 1
openstack user create --password n00rinfra ceilometer
openstack role add --project service --user ceilometer admin
openstack service create --name ceilometer --description "Telemetry" metering
openstack endpoint create --publicurl http://Cirrus-Controller:8777 --internalurl http://Cirrus-Controller:8777 --adminurl http://Cirrus-Controller:8777 --region RegionOne metering
echo
echo "--->> Installing Packages..."
echo
sleep 1
apt-get install ceilometer-api ceilometer-collector ceilometer-agent-central ceilometer-agent-notification ceilometer-alarm-evaluator ceilometer-alarm-notifier python-ceilometerclient -y
echo
echo -en " Configuring Ceilometer..."
sleep 1
echo "[DEFAULT]
verbose = True
rpc_backend = rabbit
auth_strategy = keystone

[database]
connection = mongodb://ceilometer:n00rinfra@Cirrus-Controller:27017/ceilometer        

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

[service_credentials]
os_auth_url = http://Cirrus-Controller:5000/v2.0
os_username = ceilometer
os_tenant_name = service
os_password = n00rinfra      
os_endpoint_type = internalURL
os_region_name = RegionOne

[publisher]
telemetry_secret = n00rinfra 
" > /etc/ceilometer/ceilometer.conf
echo "[OK]"
echo
echo "--->> Finalizing Ceilometer Installation..."
echo
sleep 1
service ceilometer-agent-central restart
service ceilometer-agent-notification restart
service ceilometer-api restart
service ceilometer-collector restart
service ceilometer-alarm-evaluator restart
service ceilometer-alarm-notifier restart
service glance-registry restart
service glance-api restart
service cinder-api restart
service cinder-scheduler restart
echo
echo
echo " #################################### CIRRUS Images Flavors Adjustment & Configuration ############################"
sleep 1
echo
echo
echo "--->> Configuring Cirrus Image Flavors..."
sleep 2
nova flavor-delete 1
nova flavor-delete 2
nova flavor-delete 3
nova flavor-delete 4
nova flavor-delete 5
nova flavor-create Standard.1x1 1 1024 10 1 --ephemeral 0 --swap 1024 --is-public True
nova flavor-create Standard.2x2 2 2048 20 2 --ephemeral 0 --swap 2048 --is-public True
nova flavor-create Standard.3x3 3 3072 30 3 --ephemeral 0 --swap 3072 --is-public True
nova flavor-create Standard.4x4 4 4096 40 4 --ephemeral 0 --swap 4096 --is-public True
nova flavor-create Standard.5x5 5 5120 50 5 --ephemeral 0 --swap 5120 --is-public True
nova flavor-create Standard.6x6 6 6144 60 6 --ephemeral 0 --swap 6144 --is-public True
nova flavor-create Standard.7x7 7 7168 70 7 --ephemeral 0 --swap 7168 --is-public True
nova flavor-create Standard.8x8 8 8192 80 8 --ephemeral 0 --swap 8192 --is-public True
nova flavor-create HighCPU.4x2 9 2048 40 4 --ephemeral 0 --swap 2048 --is-public True
nova flavor-create HighCPU.5x3 10 3072 50 5 --ephemeral 0 --swap 3072 --is-public True
nova flavor-create HighCPU.6x4 11 4096 60 6 --ephemeral 0 --swap 4096 --is-public True
nova flavor-create HighCPU.7x5 12 5120 70 7 --ephemeral 0 --swap 5120 --is-public True
nova flavor-create HighCPU.8x6 13 6144 80 8 --ephemeral 0 --swap 6144 --is-public True
nova flavor-create HighMEM.2x6 14 6144 60 2 --ephemeral 0 --swap 6144 --is-public True
nova flavor-create HighMEM.3x7 15 7168 70 3 --ephemeral 0 --swap 7168 --is-public True
nova flavor-create HighMEM.4x8 16 8192 80 4 --ephemeral 0 --swap 8192 --is-public True
nova flavor-create HighMEM.5x9 17 9216 90 5 --ephemeral 0 --swap 9216 --is-public True
nova flavor-create HighMEM.6x10 18 10240 100 6 --ephemeral 0 --swap 10240 --is-public True
nova flavor-create HighMEM.7x11 19 11264 110 7 --ephemeral 0 --swap 11264 --is-public True
nova flavor-create HighMEM.8x12 20 12288 120 8 --ephemeral 0 --swap 12288 --is-public True
echo
echo
echo " #################################### Fetching & Loading Operating Systems Images ############################"
echo
mkdir /tmp/images
sleep 1
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mCirros\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/cirros-0.3.4-x86_64-disk.img
glance image-create --name "OS:Cirros-0.3.4" --file /tmp/images/cirros-0.3.4-x86_64-disk.img --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mCoreOS\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/coreos_production_openstack_image.img
glance image-create --name "OS:CoreOS-766.4" --file /tmp/images/coreos_production_openstack_image.img --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mCentOS\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/CentOS-7-x86_64-GenericCloud.qcow2
glance image-create --name "OS:CentOS-7" --file /tmp/images/CentOS-7-x86_64-GenericCloud.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mFedora\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/Fedora-Cloud-Base-22-20150521.x86_64.qcow2
glance image-create --name "OS:Fedora-22" --file /tmp/images/Fedora-Cloud-Base-22-20150521.x86_64.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mSUSE Linux Enterprise\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/SUSE_Linux_Enterprise_12.x86_64-0.0.1.qcow2
glance image-create --name "OS:SUSE-12" --file /tmp/images/SUSE_Linux_Enterprise_12.x86_64-0.0.1.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mOpen SUSE\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/openSUSE-13.2-OpenStack-Guest.x86_64-0.0.10-Build1.32.qcow2
glance image-create --name "OS:OpenSUSE-13.2" --file /tmp/images/openSUSE-13.2-OpenStack-Guest.x86_64-0.0.10-Build1.32.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mUbuntu\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/Ubuntu-trusty-server-cloudimg-amd64-disk1.img
glance image-create --name "OS:Ubuntu-14.04" --file /tmp/images/Ubuntu-trusty-server-cloudimg-amd64-disk1.img --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mDebian\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/debian-8.2.0-openstack-amd64.qcow2
glance image-create --name "OS:Debian-8.2" --file /tmp/images/debian-8.2.0-openstack-amd64.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mWindows\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/windows-server-2012-r2.qcow2
glance image-create --name "OS:Windows-2012R2" --file /tmp/images/windows-server-2012-r2.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:Jenkins\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-jenkins-1.613-0-ubuntu-14.04.qcow
glance image-create --name "APP:Jenkins-1.613" --file /tmp/images/bitnami-jenkins-1.613-0-ubuntu-14.04.qcow --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:WordPress\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-wordpress-4.3.1-0-ubuntu-14.04.qcow2
glance image-create --name "APP:WordPress-4.3.1" --file /tmp/images/bitnami-wordpress-4.3.1-0-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:Joomla\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-joomla-3.4.4-1-ubuntu-14.04.qcow2
glance image-create --name "APP:Joomla-3.4.4" --file /tmp/images/bitnami-joomla-3.4.4-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:Redmine\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-redmine-3.1.1-1-ubuntu-14.04.qcow2
glance image-create --name "APP:Redmine-3.1.1" --file /tmp/images/bitnami-redmine-3.1.1-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:MediaWiki\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-mediawiki-1.25.2-1-ubuntu-14.04.qcow2
glance image-create --name "APP:MediaWiki-1.25.2" --file /tmp/images/bitnami-mediawiki-1.25.2-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:SugarCRM\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-sugarcrm-6.5.22-1-ubuntu-14.04.qcow2
glance image-create --name "APP:SugarCRM-6.5.22" --file /tmp/images/bitnami-sugarcrm-6.5.22-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:OpenERP\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-openerp-7.0-19-ubuntu-14.04.qcow2
glance image-create --name "APP:OpenERP-7.0" --file /tmp/images/bitnami-openerp-7.0-19-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:GitLab\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-gitlab-7.14.3-1-ubuntu-14.04.qcow2
glance image-create --name "APP:GitLab-7.14.3" --file /tmp/images/bitnami-gitlab-7.14.3-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:Drupal\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-drupal-7.40-0-ubuntu-14.04.qcow2
glance image-create --name "APP:Drupal-7.40" --file /tmp/images/bitnami-drupal-7.40-0-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:Alfresco\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-alfresco-5.0.d-0-ubuntu-14.04.qcow2
glance image-create --name "APP:Alfresco-5.0.d" --file /tmp/images/bitnami-alfresco-5.0.d-0-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:Piwik\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-piwik-2.14.3-1-ubuntu-14.04.qcow2
glance image-create --name "APP:Piwik-2.14.3" --file /tmp/images/bitnami-piwik-2.14.3-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mApplication:OpenProject\033[0m"; echo " <<------"
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-openproject-4.2.6-1-ubuntu-14.04.qcow2
glance image-create --name "APP:OpenProject-4.2.6" --file /tmp/images/bitnami-openproject-4.2.6-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress  
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mINFRA:Django Stack\033[0m"; echo " <<------"                        
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-djangostack-1.7.10-1-ubuntu-14.04.qcow2
glance image-create --name "INFRA:Django Stack" --file /tmp/images/bitnami-djangostack-1.7.10-1-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mINFRA:LAPP Stack\033[0m"; echo " <<------"     
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-lappstack-5.5.30-0-ubuntu-14.04.qcow2
glance image-create --name "INFRA:LAPP Stack" --file /tmp/images/bitnami-lappstack-5.5.30-0-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
echo -en "   ------>> FETCHING & LOADING "; echo  -en "\033[36;5mINFRA:LAMP Stack\033[0m"; echo " <<------"  
sleep 1
echo
wget -P /tmp/images http://192.168.0.114/openstack-images/bitnami-lampstack-5.5.30-0-ubuntu-14.04.qcow2
glance image-create --name "INFRA:LAMP Stack" --file /tmp/images/bitnami-lampstack-5.5.30-0-ubuntu-14.04.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
echo
rm -r /tmp/images
sleep 1
clear
echo
echo
echo -en " #################################### " ; echo  -en "\033[36;1mCirrus\033[0m" ; echo " OpenStack IS READY ! ############################"
sleep 1
echo
echo "   Horizon Dashbord is @ http://$MYIP/"
echo "   Username : admin"
echo "   Password : ********"
echo
echo " --> Before u begin using you have to do the following:"
echo " 1- Edit and adjust /etc/hosts based on your nodes."
echo " 2- Create Tenant & External Networks."
echo " 3- Add $MYIP to DNS Server to resolve to Cirrus-Controller.noor.net"
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

- Typical Hosts File for Controller Node is as follows.

{{{
127.0.0.1       localhost.localdomain localhost
192.168.0.130   controller.noor.net controller

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


== External Network ==

- Before Configuring Network Details, Make Sure that the SDN node is up & Running.

- To Configure External Network for CIRRUS [The Network that will provide Internet Connectivity], apply the following Commands

{{{
source /etc/keystone/admin-openrc.sh
neutron net-create ext-net --router:external --provider:physical_network external --provider:network_type flat
neutron subnet-create ext-net 192.168.38.0/24 --name ext-subnet --allocation-pool start=192.168.38.50,end=192.168.38.250 --disable-dhcp --gateway 192.168.38.1         <--- EDIT VALUES AS NEEDED
}}}

- After Configuring The External Network, Every tenant can create its network within Horizon.

= Running Services =

- For List purposes, below are the Services running on Cirrus-Controller Node

{{{
service mysql restart
service apache2 restart
service glance-registry restart
service glance-api restart
service nova-api restart
service nova-cert restart
service nova-consoleauth restart
service nova-scheduler restart
service nova-conductor restart
service nova-novncproxy restart
service neutron-server restart
service cinder-scheduler restart
service cinder-api restart
service heat-api restart
service heat-api-cfn restart
service heat-engine restart
service mongodb restart
service ceilometer-agent-central restart
service ceilometer-agent-notification restart
service ceilometer-api restart
service ceilometer-collector restart
service ceilometer-alarm-evaluator restart
service ceilometer-alarm-notifier restart
}}}
