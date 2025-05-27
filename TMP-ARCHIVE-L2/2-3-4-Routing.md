# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Routing

### PRACTICE with routing

We need 3 VMs (VM1, VM2, VM3) with second Host-Only network interface (most probably **enp0s8**).
IP address/Routing scheme is as follows.

Assign IP addresses to network interfaces (use `nmtui` for easy config)

* VM1  
  * IP `10.1.10.2/24`

* VM2  
  * IP1 `10.1.10.1/24`
  * IP2 `10.1.11.1/24`

* VM3  
  * IP `10.1.11.2/24`


Check if it works

* VM1
  * `ping 10.1.10.1`
  
* VM2 
  * `ping 10.1.10.2`
  * `ping 10.1.11.2`
  
* VM3 
  * `ping 10.1.11.1`


On VM2 run following to block **icmp redirects** since all interfaces are in one LAN, 
and VM2 will tell neighbours to connect directly otherwise.
(More on `iptables` in Level3)

* VM2
  * `iptables -A OUTPUT -p icmp -m icmp --icmp-type redirect -j DROP`

To make this command permanent (i.e. run after reboot) we can add it to `/etc/rc.local` file and make it executable

```bash
echo "iptables -A OUTPUT -p icmp -m icmp --icmp-type redirect -j DROP" >> /etc/rc.local
chmod +x /etc/rc.local

```
> NOTE ! `/etc/rc.local` file is still in place as compatible solution for previous `init`-managed systems.
> If we would like to have more modern solution, we can create separate service to run custom commands as showed in previous SystemD lesson part.


Now if you try to ping VM3 from VM1 it will not work

* VM1
  * `ping 10.1.11.2`

Why ? 


Because you need to define additional routes on leaf nodes VM1 and VM3 to see each other.
Manual commands to do this will be the following:

* On VM1
  * `ip r a 10.1.11.0/24 via 10.1.10.1`
* On VM3 
  * `ip r a 10.1.10.0/24 via 10.1.11.1`

To make them run after reboot, we can add these commands to `rc.local`, but it is not good solution.
Instead we can configure static routes with `nmtui` to keep this settings permanent.

(Run `nmtui` go to `Edit a connection`->`<interface-name>`->`IPv4 Configuration`->`Show`->Routing`->`Edit`)

As a result following file will be created:
`/etc/sysconfig/network-scripts/route-<device-name>`



Check if it works

* VM1
  * `ping 10.1.11.2`
  * `mtr 10.1.11.2`

* VM3
  * `ping 10.1.10.2`
  * `mtr 10.1.10.2`

VM2 now acts as router and redirects/forwards ip packets between hosts.

#### PACKET FORWARDING
It is important to understand that apart from routing itself 
PACKET FORWARDING is controlled by additional kernel setting `/proc/sys/net/ipv4/ip_forward`

* **1** means enabled 
* **0** means disabled

(NOTE! Default setting differs depending on Linux distribution version)

Check current setting: 

`cat /proc/sys/net/ipv4/ip_forward`

we can now disable it:

* VM2 
  * `echo 0 >/proc/sys/net/ipv4/ip_forward`

You can notice that pings (in fact any packet transfer between subnets) from VM1 to VM3 and vice versa will stop !

If we want to make this setting **permanent**, so they persist after reboot
we need to do:

* CentOS/RHEL version
```bash
echo  'net.ipv4.ip_forward = 1'  >>  /usr/lib/sysctl.d/50-default.conf
sysctl -p /usr/lib/sysctl.d/50-default.conf
```

* Ubuntu/Debian version
```bash
echo  'net.ipv4.ip_forward = 1'  >> /etc/sysctl.conf
sysctl -p /etc/sysctl.conf
```


### Network Traffic Analysis and Monitoring Tools

#### tcpdump

**tcpdump** - basic tool to troubleshoot network.<br>
<br>
<br>

#### PRACTICE
On VM1 run:
* `tcpdump -i enp0s8 host 10.1.10.2`

On VM2 run:
* `ping -c2 10.1.10.2`

Now on VM1 try another commands:
* `tcpdump -i enp0s8 src 10.1.10.2`
* `tcpdump -i enp0s8 dst 10.1.10.2`

You should see difference:
**host** filter captures both (destination) & (source) traffic.
**src** / **dst** - only packets going one way. 
<br>
<br><br>
You can also capture whole subnet traffic:
* `tcpdump -i enp0s8 net 10.1.10.0/24`

Or only traffic to/from specific port.<br>
On VM1 run:
* `tcpdump -i enp0s8 dst port 22`

On VM2 run:
* `ssh 10.1.10.2`
<br>
<br>

We can show IP/Port in numbers.

On VM1 run:
* `tcpdump -i enp0s8 -nn -v dst port 80`

On VM2 run:
* `telnet 10.1.10.2 80` <br>
Options:<br>
**-nn** : 
_A single (n) will not resolve hostnames. A double (nn) will not resolve hostnames or ports. This is handy for not only viewing the IP / port numbers but also when capturing a large amount of data, as the name resolution will slow down the capture._<br>
**-v** : 
_Verbose, using (-v) or (-vv) increases the amount of detail shown in the output, often showing more protocol specific information._

Or see ICMP traffic only.<br>

On VM1 run:
* `tcpdump -i enp0s8 icmp`

On VM2 run:
* `ping -c2 10.1.10.2` <br>

#### iftop

**iftop** - interactive interface monitor tool.

Install `iftop`

CentOS/RHEL: 
`yum -y install iftop`

Ubuntu/Debian: 
`apt install iftop`


Keywords:
* **n** – toggles DNS hostnames resolution 
* **N** – toggles port names resolution
* **p** – toggles port numbers display
* **P** – pauses the screen
* **t** – toggles display modes
* **h** – toggles the help screen (more options here)

Simply run:

```bash
iftop
```

Show traffic for particular interface:
(you can specify interface with `-i`, but if not specified it tries to find the one that looks like an external interface)

```bash
iftop -i lo
```

```bash
ping 127.1.2.3 >/dev/null & 
ping 127.4.5.6 > /dev/null &  
ping 127.7.8.9 > /dev/null &
iftop -i lo
```

Now let's filter some traffic
type `f` and then `dst host 127.7.8.9`
You will need to wait for few seconds and then filter will work.

On another terminal run
```bash
telnet 127.7.8.9 22
```
Now you can see that session too.



The same can be done with command line option 
```bash
iftop -i lo -f dst host 127.7.8.9
```


Disable resolving hostnames (-n) and port numbers (-N):

```bash
iftop -nN -i enp0s8
```

Use text interface:

`iftop -t`

Display bandwidth rates in bytes/sec instead of bits/sec

`iftop -B`

Show traffic for specific subnet

`iftop -F 10.1.10.0/24`

More info about `iftop` can be found in `man iftop`
