{{{
Author       : Mohamed Ashraf
Title        : Server Load Balancer module
Version      : 1.0
Creation date: 23/11/2014
Latest update: 23/11/2014
}}}

<<TableOfContents(4)>>


{{attachment:load_balancing.png}}



= Introduction =

The Tool that is used for Load balancing soultion is "HAProxy"

HAProxy, which stands for High Availability Proxy, is a popular open source software TCP/HTTP Load Balancer and proxying solution.

Its most common use is to improve the performance and reliability of a server environment by distributing the workload across multiple servers (e.g. web, application, database). 

It is used in many high-profile environments, including: GitHub, Imgur, Instagram, and Twitter.

HAProxy features Layer 4 TCP/IP and Layer 7 [ex:HTTP] Load balancing featuers.

= Types of Load Balancing =

== No Load Balancing ==

A simple web application environment with no load balancing might look like the following:

{{attachment:1.png}}

The user connects directly to the server, at yourdomain.com and there is no load balancing. 

If your single web server goes down, the user will no longer be able to access your web server. 

Additionally, if many users are trying to access your server simultaneously and it is unable to handle the load, they may have a slow experience or they may not be able to connect at all.

== Layer 4 Load Balancing ==

Load balancing this way will forward user traffic based on IP range and port. 

If a request comes in for yourdomain.com/anything, the traffic will be forwarded to the backend that handles all the requests for yourdomain.com on port 80.

{{attachment:2.png}}

The user accesses the load balancer, which forwards the user's request to the web-backend group of backend servers. 

Whichever backend server is selected will respond directly to the user's request. 

Generally, all of the servers in the web-backend should be serving identical content, otherwise the user might receive inconsistent content.

== Layer 7 Load Balancing ==

Using layer 7 allows the load balancer to forward requests to different backend servers based on the content of the user's request. 

This mode of load balancing allows you to run multiple web application servers under the same domain and port.

{{attachment:3.png}}

In this example, if a user requests yourdomain.com/blog, they are forwarded to the blog backend, which is a set of servers that run a blog application. Other requests are forwarded to web-backend, which might be running another application.

= Load Balancing Algorithms =

The load balancing algorithm that is used determines which server, in a backend, will be selected when load balancing. 

In addition to the load balancing algorithm, servers can be assigned a weight parameter to manipulate how frequently the server is selected, compared to other servers.

HAProxy offers several options for algorithms, which are:

== Round Robin ==

Round Robin selects servers in turns. This is the default algorithm.

== Leastconn ==

Selects the server with the least number of connections.

It is recommended for longer sessions. 

Servers in the same backend are also rotated in a round-robin fashion.

== Source ==

This selects which server to use based on a hash of the source IP i.e. your user's IP address. 

This is one method to ensure that a user will connect to the same server.

= Health Check =

HAProxy uses health checks to determine if a backend server is available to process requests. 

This avoids having to manually remove a server from the backend if it becomes unavailable. 

The default health check is to try to establish a TCP connection to the server i.e. it checks if the backend server is listening on the configured IP address and port.

If a server fails a health check, and therefore is unable to serve requests, it is automatically disabled in the backend i.e. traffic will not be forwarded to it until it becomes healthy again. 

If all servers in a backend fail, the service will become unavailable until at least one of those backend servers becomes healthy again.



= Load Balancer Installation =

For ubuntu/debian based Linux systems, and as root run the below command

{{{
apt-get install haproxy
}}}

Configuration file can be found at

{{{
/etc/haproxy/haproxy.cfg
}}}

Next we need to configure an initialization script for it to run at system start-up

create a script @/etc/init.d/load-balancer

We have pre-created the script for you, just copy below into your created file.

{{{
#!/bin/sh -e
# Start or stop Filter Cluster SMTP Load BALANCER


# By Mohamed Ashraf



### BEGIN INIT INFO


# Provides:          Load Balancer (Haproxy)

# Required-Start:    Configuration file of ha

# Required-Stop:     Configuration file of ha

# Short-Description: Start Filter Cluster Load Balancer

# Description:       Enable service provided by daemon.


### END INIT INFO
PATH=/bin:/usr/bin:/sbin:/usr/sbin
case "$1" in
start)
echo
echo "Starting Filter Cluster SMTP Load Balancer.."
echo
sleep 2
/usr/local/sbin/haproxy -f /etc/haproxy/haproxy.cfg
echo
echo "Balancer Activated!"
echo
sleep 1
;;

stop)
echo
echo -n "Stopping Filter Cluster SMTP Load Balancer.."

pkill haproxy -f
sleep 2
echo "Balancer Stopped!"
echo
sleep 1
;;

restart)
$0 stop || true
$0 start
;;
*)
echo "Usage: /etc/init.d/haproxy {start|stop|restart}"


exit 1


;;
esac
exit 0
}}}

Make the script executable

{{{
chmod a+x /etc/init.d/load-balancer
}}}

Initialize the Script so its run by the kernel at system boot

{{{
update-rc.d load-balancer defaults
}}}

Start the load balancer

{{{
/etc/init.d/load-balancer start
}}}


= NOOR's Load Balancer =

A layer 4 TCP Balancer has been implemented in the SMTP Filter Cluster Project, which can operate in two modes:

== Non Transparent Mode ==

Where the request source IP is irrelevant to the service provided, in this case the Load balancer Module sends requests to the backed server using it's IP address as source.

The configuration should be as below '''@/etc/haproxy/haproxy.cfg'''

{{{

global
        log 127.0.0.1   local0
        log 127.0.0.1   local1 notice
        maxconn 6000
        chroot /var/lib/haproxy
        user root
        group root
        daemon
#       debug
#       quiet

frontend FRONT-FACING
        bind 0.0.0.0:25
        mode tcp
        no option http-server-close
        timeout client 1m
        log global
        option tcplog
        default_backend TO-FILTER-CLUSTER

backend TO-FILTER-CLUSTER
        mode tcp
        no option http-server-close
        log global
        option tcplog
        timeout server 1m
        timeout connect 5s
        balance roundrobin

        server filter1 10.10.10.1:25 weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
        server filter2 10.10.10.2:25 weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
        server filter3 10.10.10.3:25 weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
}}}

== Transparent Mode ==

Where it is important that the serving app/server knows the client origin IP, in this setup the Load Balancer will act as a transparent proxy.

The configuration should be as below

{{{

global
        log 127.0.0.1   local0
        log 127.0.0.1   local1 notice
        maxconn 6000
        chroot /var/lib/haproxy
        user root
        group root
        daemon
#       debug
#       quiet

frontend FRONT-FACING
        bind 0.0.0.0:25 transparent
        mode tcp
        no option http-server-close
        timeout client 1m
        log global
        option tcplog
        default_backend TO-FILTER-CLUSTER

backend TO-FILTER-CLUSTER
        source 0.0.0.0 usesrc clientip
        mode tcp
        no option http-server-close
        log global
        option tcplog
#       option smtpchk HELO noor.net
        timeout server 1m
        timeout connect 5s
        balance roundrobin

        server filter1 10.10.10.1:25 send-proxy weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
        server filter2 10.10.10.2:25 send-proxy weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions
        server filter3 10.10.10.3:25 send-proxy weight 100 check inter 6000 minconn 0 maxconn 0 on-marked-down shutdown-sessions

}}}

'''WHERE:'''

send-proxy : Related to the Transparent Proxy installation, tells the balancer to forward the Soure IP to the serving server

weight     : Priority weight of each server

Check      : Check the health of the server

inter      : Health Check Interval (ms)

minconn    : Minimum allowed simultaneous Connections to the server

maxconn    : Maximum allowed simultaneous Connections to the server

on-marked-down shutdown-sessions : Shutsdown Connections session the the server, when the health check fails.


- Add the below lines @/etc/sysctl.conf

{{{
##Added for TPROXY#####                
net.ipv4.ip_forward = 1
kernel.sem = 250 32000 100 128
kernel.shmmax = 536870912
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.eth0.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
net.ipv4.conf.lo.send_redirects = 0
}}}
