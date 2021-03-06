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

Most of the modern Linux distributions will have first network interface name 
`enp0s3` <br>
Second network interface most probably will be `enp0s8`

`enp0s3` meaning:<br />
**en** - ethernet<br />
**p0** - peripheral/prefix/bus number 0<br />
**s3** - slot/device number 3<br />

There are different naming schemes supported by **systemd**. Interface name examples:<br>
* `ens33`, `ens192`
* `eno16780032`
* `enx78e7d1ea46da`

Recent distributions have special _man_ for it: `man systemd.net-naming-scheme`

More details can be found at: <br />
* https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/ch-consistent_network_device_naming#sec-Naming_Schemes_Hierarchy<br>
* https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/ <br />
* https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-understanding_the_predictable_network_interface_device_names <br />

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
There is old, deprecated `ifconfig` command, that should be avoided to use.

### ARP Table
`arp` command allows to see current ARP table (`arp –an`)  
It is old command and the new equivalent is: <br>
`ip n[eigh]`

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

Linux **routing table** is obtained by one of the following commands:
* `/sbin/ip r`
* `/bin/netstat -rn` 
* `/sbin/route -n`

Last two commands are old, deprecated should be avoided to use.

#### PRACTICE
1. Create second network interface on two VMs in VirtualBox. 
Assign each one to `VirtualBox Host-Only Ethernet Adapter`<br>
After starting VMs use `nmtui` to manually assign IP addresses 
to second interface on each VM:
* **10.10.10.10/24** - on VM1
* **10.10.10.11/24** - on VM2<br><br>
After that you should be able to `ping` from one VM to another by these IPs
and even connect another VMs port.

2. Manually assign another IP addresses to the same interfaces:

* `ip a a 172.16.11.5/24 brd + dev enp0s8` - on VM1
* `ip a a 172.16.11.6/24 brd + dev enp0s8` - on VM2<br><br>
After that you should be able to `ping` from one VM to another by these IPs 
and even connect another VMs port.


### Network Port Management

It is not enough just to deliver packets from one host to another. 
Information should be delivered to from one process/program on one host
to another process/program on another host.
That is why not one, bur **TWO** pairs of identifiers play important role:

|  _SourceAddress_ | _DestinationAddress_ 
| --- | --- |
|  **_SourcePort_** |  **_DestinationPort_**

Second pair presents **UDP/TCP port numbers**. 
It is 16 bit number (**0-65535**). 
Each UDP/TCP packet (on transport layer) contains source & destination port.  
This allows to uniquely identify a conversation between processes.

Many ‘well known’ ports published for client-server applications can be found in: 
`/etc/services` file. Some examples are below:

|  TCP Port | Service 
| --- | --- |
| 20,21 | FTP
| 22 | SSH (remote access)
| 25 | SMTP (mail)
| 80 | HTTP (web)
| 143| IMAP (mail)
| 443| HTTPS (HTTP over SSL/TLS)
| 465| SMTPS (SMTP over SSL/TLS
| 993 | IMAPS (IMAP over SSL/TLS) 


* `netstat -nlpt` - Current TCP ports and appropriate processes listening
* `netstat -nlpu` - Current UDP ports and appropriate processes listening
* `netstat -ant`  - Active TCP connections
* `netstat -anu`  - Active UDP connections
* `ss -nlpt` - Alternative command to `netstat`

### Network Tools

* `ping`
* `traceroute`
* `mtr`

Examples:
* `ping -nc3 ya.ru`
* `traceroute -n goo.gl`
* `mtr yahoo.com`
* `mtr -nrc 1 fb.com`

Options:
* **-n**	_No DNS (Do not try to resolve the host names)_
* **-r** 	_put mtr into “wide report mode” (report after the number of cycles specified by the 
-c option (default 10) wide allows not to cut hostnames in the report)_
* **-c N**  _set the number of pings sent to determine both the machines on 
the network and the reliability of those machines. Each cycle lasts one second_
* **-u**	_Use UDP datagrams instead of ICMP ECHO (useful if “ICMP limiting” is found somewhere)_


### Network Traffic Monitor Tools

**tcpdump** - basic tool to troubleshoot network.<br>
**iftop** - interactive interface monitor tool.
<br>
<br>

#### PRACTICE
On VM1 run:
* `tcpdump -i enp0s8 host 10.10.10.11`

On VM2 run:
* `ping -c2 10.10.10.10`

Now on VM1 try another commands:
* `tcpdump -i enp0s8 src 10.10.10.11`
* `tcpdump -i enp0s8 dst 10.10.10.11`

You should see difference:
**host** filter captures both (destination) & (source) traffic.
**src** / **dst** - only packets going one way. 
<br>
<br><br>
You can also capture whole subnet traffic:
* `tcpdump -i enp0s8 net 10.10.10.0/24`

Or only traffic to/from specific port.<br>
On VM1 run:
* `tcpdump -i enp0s8 dst port 22`

On VM2 run:
* `ssh 10.10.10.10`
<br>
<br>

We can show IP/Port in numbers.

On VM1 run:
* `tcpdump -i enp0s8 -nn -v dst port 80`

On VM2 run:
* `telnet 10.10.10.10 80` <br>
Options:<br>
**-nn** : 
_A single (n) will not resolve hostnames. A double (nn) will not resolve hostnames or ports. This is handy for not only viewing the IP / port numbers but also when capturing a large amount of data, as the name resolution will slow down the capture._<br>
**-v** : 
_Verbose, using (-v) or (-vv) increases the amount of detail shown in the output, often showing more protocol specific information._

Or see ICMP traffic only.<br>

On VM1 run:
* `tcpdump -i enp0s8 icmp`

On VM2 run:
* `ping -c2 10.10.10.10` <br>

