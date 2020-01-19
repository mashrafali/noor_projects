{{{
Author       : Mohamed Ashraf
Title        : FortiGate UTM Initial Configuration
Version      : 1.0
Creation date: 26/8/2015
Latest update: 26/8/2015
}}}


<<TableOfContents(4)>>



{{attachment:logo1.jpg}}

= Configurtion =

- To configure the Box the following has been applied:

== Initial Cli Configuration ==

=== Management Interface ===

{{{
config system interface
edit port1
set ip 192.168.0.100 255.255.255.0
append allowaccess http
end
}}}

=== Configure Default GW ===

{{{
config router static
edit 1
set device port1
set gateway <class_ip>
end
}}}

=== Configure DNS ===

{{{
config system dns
set primary <Primary DNS server>
set secondary <Secondary DNS server>
end
}}}

=== Upload License ===

To upload the FortiGate VM license from an FTP or TFTP server, use the following CLI command:

{{{
execute restore vmlicense {ftp | tftp} <VM license file name> <Server IP or FQDN>[:server port]
}}}

== Configuration File Upload ==

- After Accessing the Box via HTTP, proceed to upload the following configuration file in order to load NOOR's default configuration profiles for the following Services:

1- Anti Virus NOOR Default Profile

2- Web Filter NOOR Default Profile

3- Application Control NOOR Default Profile

4- Intrusion Protection NOOR Default Profile

5- Email Filter NOOR Default Profile

6- Data Leak Prevention NOOR Default Profile

7- VOIP NOOR Default Profile

'''Config File:'''

''- Download File:''

[[attachment:NOOR-FG-1.conf]]

''- File View:''

{{attachment:NOOR-FG-1.conf}}
