# Linux Administration and Networking Basics (level 2) Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Networking basics

#### Predictable Network Interface Names
Current version of **systemd/udev** (starting with **v197**) automatically assign predictable, 
stable network interface names for all local network interfaces. 
This is a departure from old traditional interface naming scheme 
("**eth0**", "**eth1**", "**wlan0**", ...).

Current network interfaces names can be listed this way:

`ip l | grep mtu | sed 's/://g' | awk '{print $1,$2}' `

or

`nmcli d`

`nmcli c`


The names have two-character prefixes based on the type of interface:
`en` for Ethernet,
&nbsp; 
`wl` for wireless LAN (WLAN),
&nbsp;
`ww` for wireless wide area network (WWAN).

Most of the modern Linux distributions will have first network interface name 
`enp0s3`. Second network interface most probably will be `enp0s8`.

`enp0s3` meaning can be expaned as: 

**en** - ethernet 

**p0** - peripheral/prefix/bus number 0

**s3** - slot/device number 3

More info: https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-understanding_the_predictable_network_interface_device_names

Recent distributions have special _man_ for it:

```bash
man systemd.net-naming-scheme
```


Configuration of network interfaces (`/etc/sysconfig/`).  Important Network Files.
Configuring resolver

Network Configuration with and without NetworkManager
CentOS 7 comes with Network Manager, a service that runs by default and has a graphical, text as well as command line interface. You can use Network Manager to create, edit and remove interfaces and it can be used to configure Ethernet, WiFi, Mobile, DSL and PPPoE connections. Using the GUI is pretty straight forward. To invoke the text interface just type:
```bash
nmtui
```
The command line interface can be invoked by the command nmcli. For example to get a list if all NICs (Ethernet cards):
```bash
nmcli d
```
However you may not want to run NetworkManager service on your server because of various reasons. Advanced configuration options are limited and it does not yet support some device types like ISDN, IPSec to name a few. If you decide to disable NetworkManager service and use just the network service, you can configure your network by editing some config files and restarting the network service.


#### List all network interfaces
To list and manipulate devices, routing, tunnels etc you can use the ip command.
```bash
ip link show
```

#### Configure DHCP on an interface
Edit the config files in `/etc/sysconfig/network-scripts` directory. In this directory, each device should have a configuration file named ifcfg-<device-name>. If you want to configure DHCP on device enp0s3, edit the file ifcfg-enp0s3
For the device to be activated at startup time ste ONBOOT=yes
To enable DHCP, set BOOTPROTO=dhcp
Leave the other settings as such and restart networking
```bash
systemctl restart network
```

#### Configure static IP
Edit the config file `/etc/sysconfig/network-scripts/ifcfg-<devicename>`. Using the example interface enp0s3 from above, the file to edit is /etc/sysconfig/network-scripts/ifcfg-enp0s3. Change ONBOOT to "yes" if you want interface to be UP on boot. Change BOOTPROTO to "static" and set a IP address and network mask as shown below.
```bash
ONBOOT=yes
BOOTPROTO=static
IPADDR=10.16.1.108
NETMASK=255.255.255.0
You could also add a Gateway address as below.
GATEWAY=10.16.1.1
```

The default gateway can also be added to /etc/sysconfig/network file like below:
```bash
NETWORKING=yes
GATEWAY=10.16.1.1
Remember to restart the network service
```

```bash
systemctl restart network
```

The ip command can be used to verify the settings
```bash
ip addr
```

The `ip` command can be also used to change IP addresses, but the change won't be permanent. For permanent changes you need to edit the config files and restart networking.


#### IP Networking Control Files

Different linux distribution vendors put their networking configuration files in different places in the filesystem. Here is a brief summary of the locations of the IP networking configuration information under a few common linux distributions along with links to further documentation. 
Location of networking configuration files
•	RedHat/CentOS
o	Interface definitions 				 /etc/sysconfig/network-scripts/ifcfg-<interface-name> 
o	Hostname and default gateway definition /etc/sysconfig/network 
o	Definition of static routes 			 /etc/sysconfig/static-routes 
/etc/resolv.conf
This file specifies the IP addresses of DNS servers and the search domain. 
Unless configured to do otherwise, the network initialization scripts populate this file. For more information about this file, refer to the resolv.conf man page.

/etc/hosts
The main purpose of this file is to resolve hostnames that cannot be resolved any other way. It can also be used to resolve hostnames on small networks with no DNS 
server. 

The use of this file before DNS resolving (/etc/resolv.conf) is defined in /etc/nsswitch.conf on line 
hosts:      files dns

Scripts to brind network interfaces up and down:
/etc/sysconfig/network-scripts/ifup
/etc/sysconfig/network-scripts/ifdown
The easiest way to manipulate all network scripts simultaneously is to use the /sbin/service command on the network service (/etc/rc.d/init.d/network), as illustrated the following command: 
/sbin/service network <action>
Here, <action> can be either start, stop, or restart. 
To view a list of configured devices and currently active network interfaces, use the following command: 
/sbin/service network status

Make sure NetworkManager is on

systemctl start NetworkManager
systemctl enable NetworkManager 

(nmtui & nmcli /nmcli help/ commands can be used for manual text-mode configuration)

Routing. Routing tables.

/bin/netstat -rn 
/bin/netstat -nlpt
/bin/netstat -nlpu
/bin/netstat -an
/sbin/route -n
/sbin/ip a
/sbin/ip r
/bin/ss -nlpt

#Manual IP address assignment
ip a a 172.16.1.189/24 brd + dev eth0

#Manual Default Gateway assignment
ip r a default via 172.16.1.2

IP packet forwarding should be enabled if the server has more than one network intefaces:  
sysctl -w net.ipv4.ip_forward=1

You can make sure the change will persist after reboot by
echo  'net.ipv4.ip_forward = 1'  >>  /usr/lib/sysctl.d/50-default.conf
sysctl -p /usr/lib/sysctl.d/50-default.conf  

Tools for checking connection with another computer on the network:
ping, traceroute, mtr

MTR
mtr -nrc 5 yahoo.com
mtr -unrc 5 yahoo.com
mtr -i 0.2 -n yahoo.com


-n	No DNS (Do not try to resolve the host names)
-rw 	put mtr into “wide report mode” (report after the number of cycles specified by the 
-c option (default 10) wide allows not to cut hostnames in the report)
-c N	set the number of pings sent to determine both the machines on the network and the reliability of those machines.  Each cycle lasts one second
-u	Use UDP datagrams instead of ICMP ECHO (useful if “ICMP limiting” is found somewhere)
-i N	number of seconds between requests (default - one second)

arp command allows to see current ARP table  (arp –an)  
new variant is: 
ip n[eigh]
 



