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


### Configure static IP manually 

Create the config file `/etc/sysconfig/network-scripts/ifcfg-<devicename>`. 

Let's assume you have new network interface with name `enp0s8`

You need to create the file
`/etc/sysconfig/network-scripts/ifcfg-enp0s8`

Minimum information you need there is:
```bash
DEVICE="enp0s8"
ONBOOT=yes
IPADDR=10.11.12.13
PREFIX=24
NM_CONTROLLED="yes"
```
Setting `ONBOOT` to "yes" will enable interface to be UP on boot.
Setting `NM_CONTROLLED` to "yes" will enable managing interface by NetworkManager.

You could also add a Gateway address as below.
```bash
GATEWAY=10.11.12.1
```
More settings can be found in config file for interface `enp0s3` located:
`/etc/sysconfig/network-scripts/ifcfg-enp0s3`


The default gateway can also be added to `/etc/sysconfig/network` file like below:
```bash
NETWORKING=yes
GATEWAY=10.11.12.1
```

Remember to restart the NetworkManager service:
`systemctl restart NetworkManager`

Other way is to bring some particular interface down and up
(in **CentOS 8** You may need to install `yum install NetworkManager-initscripts-updown` )

`ifdown enp0s8`

`ifup enp0s8`

The ip command can be used to verify the settings

`ip a`

### Network interface configuration for Ubuntu

Recent version of Ubuntu Linux have network interface configuration via **Netplan**
Since Ubuntu 20.04 Netplan replaces the traditional method of configuring network interfaces using the `/etc/network/interfaces`
(Although it is possible to switch back to old network setup also )
https://linuxconfig.org/how-to-switch-back-networking-to-etc-network-interfaces-on-ubuntu-20-04-focal-fossa-linux
but it is better to stay up-to date)

Network interface configurations are to be in `/etc/netplan` directory in the for of .yaml files

Example of configuration `/etc/netplan/00-installer-config.yaml`

```bash
network:
  ethernets:
    enp0s3:
      dhcp4: true
  version: 2
```

Let's add second interface `enp0s8` and assign static ip address.
We need to add following section under `ethernets`

```bash
   enp0s8:
      addresses:
        - 10.11.12.10/24
```


Note! Yaml file should be strict in the format.

Resulting file will have the following look:

```bash
network:
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      addresses:
        - 10.11.12.10/24
  version: 2
```

Now we need to apply changes.

We can just run

`netplan apply`

but it might be more useful to track the changes:

`netplan --debug apply` 

Next we can check the the configuration:

`ip a`

#### Managing Ubuntu network config via Network Manager

Although it's not default config, it's possible to manage Ubuntu network config via Network Manager
In order to do that we need first install Network Manager

`apt install network-manager`

Next we need to tell Netplan that we wan to manage config via Network Manager. 
This can be done by adding 
```bash
renderer: NetworkManager
```
line to .yaml config after `network:`

(Default renderer is `renderer: networkd` but it can be omited as default)


Resulting file should look like:
```bash
network:
  renderer: NetworkManager
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      addresses:
        - 10.11.12.10/24
  version: 2
```

Next we apply changes:

`netplan --debug apply` 

And we can now use `nmtui` to manage interfaces


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
