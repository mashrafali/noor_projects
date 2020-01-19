{{{
Author       : Mohamed Ashraf
Title        : NOOR's FortiGate Network Subnets
Version      : 1.0
Creation date: 16/9/2015
Latest update: 17/9/2015
}}}


= FortiGate Dedicated Subnets =

||           Name            ||    Subnet                     ||
||         Main Mangment     || 217.139.242.2/255.255.255.252 ||
||      Backup Mangment      || 172.17.15.222/255.255.255.252 ||


= Managed Internet Customer Subnets =

||Customer Name  || Inter-Vdom IPs  (/30)     || Inter-Vdom Vlan|| External Vlan || Public Subnet                     ||  Internet Bandwidth ||
|| Masria Cars   || 10.21.32.1 - 10.21.32.2   ||      2001      ||     3001      || 41.187.9.104 - 41.187.9.111       ||       3 Mbps        ||
||  Alshaya      || 10.21.32.5 - 10.21.32.6   ||      2002      ||     3002      || 217.139.135.48 - 217.139.135.55   ||       3 Mbps        ||
|| MisrPharma    || 10.21.32.9 - 10.21.32.10  ||      2003      ||     3003      || 217.139.135.65 - 217.139.135.71   ||     3.375 Mbps      ||
|| LA-Poire      || 10.21.32.13 - 10.21.32.14 ||      2004      ||     3004      || 217.139.12.224 - 217.139.12.231   ||       8 Mbps        ||
||  Roshdy       || 10.21.32.17 - 10.21.32.18 ||      2005      ||     3005      || 217.139.9.8 - 217.139.9.15        ||       9 Mbps        ||
|| Daihatsu      || 10.21.32.21 - 10.21.32.22 ||      2006      ||     3006      || 217.139.15.32 - 217.139.15.47     ||       4 Mbps        ||
|| Ezz Eldin     || 10.21.32.25 - 10.21.31.26 ||      2007      ||     3007      || 217.139.178.208 - 217.139.178.215 ||       1 Mbps        ||
|| Toy World     || 10.21.32.29 - 10.21.31.30 ||      2008      ||     3008      || 217.139.15.104 - 217.139.15.111   ||       6 Mbps        ||
|| Modern Motors || 10.21.32.33 - 10.21.32.34 ||      2009      ||     3009      || 217.139.135.80 - 217.139.135.87   ||       3 Mbps        ||

= Utilization Statistics =
|| Vdoms Used || FG Vlans Used || BGP Advertized Subnets   || Total Bandwidth Provisioned || (Provisioned/Available) Bandwidth Ratio || Max Concurrent Sessions possible ||
||    10      ||   16          || 9 (8 customers + Cpanel) ||        37.375 Mbps          ||                  3.65 %                 ||              160,000             ||


'''Console Server Sim : Vodafone-8920022031513085282'''

'''VTAP 525 :10.77.8.102'''
