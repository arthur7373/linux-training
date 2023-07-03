# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Routing

### PRACTICE with routing

We need 3 VMs (VM1, VM2, VM3) with second Host-Only network interface (most probably **enp0s8**).
IP address/Routing scheme is as follows.

Assign IP addresses to network interfaces (use `nmtui` for easy config)

* VM1 enp0s8 
  * IP `10.1.10.2/24`

* VM2 enp0s8 
  * IP1 `10.1.10.1/24`
  * IP2 `10.1.11.1/24`

* VM3 enp0s8 
  * IP `10.1.11.2/24`


Check if it works

* VM1 - `ping 10.1.10.1`
* VM2 - `ping 10.1.10.2`
* VM2 - `ping 10.1.11.2`
* VM3 - `ping 10.1.11.1`


On VM2 run following to block **icmp redirects** since all interfaces are in one LAN, 
and VM2 will tell neighbours to connect directly otherwise.

* VM2 - `iptables -A OUTPUT -p icmp -m icmp --icmp-type redirect -j DROP`

Now we need to define routes on VM1 and VM3 to see each other

* VM1 `ip a a 10.1.11.0/24 via 10.1.10.1`
* VM3 `ip a a 10.1.10.0/24 via 10.1.11.1`

Check if it works

* VM1 - 

`ping 10.1.11.2`  
`mtr 10.1.11.2`
* VM3 - 

`ping 10.1.10.2`  
`mtr 10.1.10.2`

VM2 now acts as router and redirects/forwards ip packets between hosts.
It is important to understand that apart from routing itself 
PACKET FORWARDING is controlled by additional kernel setting `/proc/sys/net/ipv4/ip_forward`

Check that setting (on recent Linux versions it is enabled = set to **1**):
`cat /proc/sys/net/ipv4/ip_forward`

we can now diasble it:

VM2 `echo 0 >/proc/sys/net/ipv4/ip_forward`

Note that pings from VM1 to VM3 and vice versa will stop !

If we want to make this setting permanent we need to do:
```bash
echo  'net.ipv4.ip_forward = 1'  >>  /usr/lib/sysctl.d/50-default.conf
sysctl -p /usr/lib/sysctl.d/50-default.conf
```


#### Task

Make static routes on VM1 and VM3 permanent, so they persist after reboot. 
Use `nmtui` for that.



### Network Traffic Monitor Tools

**tcpdump** - basic tool to troubleshoot network.<br>
**iftop** - interactive interface monitor tool.
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

Install `iftop`
`yum install iftop`

or

`apt install iftop`


Show traffic for particular interface:

`iftop -i enp0s8`

Do not resolve hostnames (-n) and port numbers (-N):


`iftop -nN -i enp0s8`

Use text interface:

`iftop -t`


Show traffic for specify subnet

`iftop -F 10.1.10.0/24`
