# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Networking basics

### Predictable Network Interface Names
For a long time Linux kernel was detecting network devices and assigning them 
interface names "**eth0**", "**eth1**", "**wlan0**", etc, 
which became traditional, but not so flexible.

Starting with **v197** current versions of **systemd** suite (and its part - **udev** device manager) automatically assign predictable, 
stable _network interface names_ for all local network interfaces. 

The names have two-character prefixes based on the type of interface:<br />
`en` for Ethernet,<br />
`wl` for wireless LAN (WLAN),<br />
`ww` for wireless wide area network (WWAN)<br />

Most of the modern Linux distributions will have first network interface name 
`enp0s3`. <br />
Second network interface most probably will be `enp0s8`.

`enp0s3` meaning:<br />
**en** - ethernet<br />
**p0** - peripheral/prefix/bus number 0<br />
**s3** - slot/device number 3<br />

More info: <br />
https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/ <br />
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-understanding_the_predictable_network_interface_device_names <br />

Recent distributions have special _man_ for it:<br />
```bash
man systemd.net-naming-scheme
```

### Configuration of network interfaces. <br />  

#### List all network interfaces
Several ways to list interfaces:<br />
* `ip l | grep mtu | sed 's/://g' | awk '{print $1,$2}' `
* `nmcli d`
* `nmcli c`

#### NetworkManager
Most modern Linux versions come with **Network Manager**, 
a service that runs by default and has a graphical, 
text as well as command line interface.

You can check if **Network Manager** is enabled and running:<br>
`systemctl is-enabled NetworkManager`<br>
`systemctl | grep NetworkManager`<br>
or<br>
`systemctl status NetworkManager`<br>

`nmtui` is  **Network Manager** text interface to create, edit and remove interfaces.
It can be used to configure Ethernet, WiFi and other connection types. 

### Difference between Linux distributions and release versions

Network configuration is one of the most different part between various Linux distributions and even release versions of the same distros.
For example, Ubuntu 16.04 and Ubuntu 18.04 configuration is completely different.
Most useful way to find more information about particular network configuration detail, is searching the Internet resources.

Below are presented some examples for CentOS Linux.
CentOS network configuration is primarily located in `/etc/sysconfig/`
Ubuntu versions have network configuration in `/etc/NetworkManager/system-connections/` in case it's managed by **NetworkManager**. 
Otherwise depending on version configuration differs.
Starting from Ubuntu 18.04 manual network configuration is managed by **netplan** and is located in `/etc/netplan`

The **ip** command can be used to check current settings:
```bash
ip a
```

The `ip` command can be also used to change IP addresses, 
but the change won't be permanent. 
For permanent changes you need to edit the config files and restart networking.

### Important Network Files.

* `/etc/resolv.conf` This file specifies the IP addresses of DNS servers. 
Depending on the way you configure network it may be possible to manually 
edit it or not.Unless configured to do otherwise, the network initialization
scripts. More info can be found in the file itself, 
under comment lines in the top.


* `/etc/hosts` Main purpose of this file is to specify hostnames without DNS.
The use of this file before DNS resolving `/etc/resolv.conf` is defined in 
`/etc/nsswitch.conf` on line: <br> 
`hosts:      files dns`


### Routing. Routing tables.

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
 



