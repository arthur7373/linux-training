# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## Packet filtering, Firewalld

Linux kernel includes network firewall capabilities to filter packets (called **netfilter**).

To use these capabilities different Linux distributions and versions use different tools.

* **IPtables**
* **Firewalld**
* **Uncomplicated Firewall (ufw)**

### IPtables
IPtables is initial tool to manage packet filtering, but newer Linux versions
provide other "front-end" tools for `iptables`. 

For example **CentOS 7/8** come with an alternative service called `firewalld`
which fulfills this same purpose & **Ubuntu** versions now use `ufw` (Uncomplicated Firewall).

First we will get understanding of `iptables`, since it's anyway remaining
at the bottom of any modern netfilter-based Linux firewall.


`iptables -L -v -n`

Will give the list of all current active **chains** & **rules**. 

![img.png](img.png)

You will see the chains and

Chain INPUT
Chain FORWARD
Chain OUTPUT
