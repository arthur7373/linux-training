# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## Proxy server (Squid)


Squid (http://www.squid-cache.org/) is a caching proxy for the Web supporting HTTP, HTTPS, FTP, and more. It reduces bandwidth and improves response times by caching and reusing frequently-requested web pages. Squid has extensive access controls and makes a great server accelerator.

Install Squid:

```bash
yum -y install squid
```

Config directory is:   `/etc/squid/` 
Main configuration file is: `/etc/squid/squid.conf`

Enable and start:
```bash
systemctl enable --now squid 
```

Check existence:
```bash
pstree | grep squid
```

Check which port is squid listening:
```bash
netstat -nlpt | grep squid
```
After installation with default configuration Squid is ready 
to serve the clients if they connect from the internal LAN IP addresses (defined in RFC1918 https://tools.ietf.org/html/rfc1918): 
* 10.0.0.0/8 
* 172.16.0.0/12 
* 192.168.0.0/16 

To use proxy server 

Set proxy variable:
```bash
export http_proxy=http://127.0.0.1:3128
```

Check
```bash
http://all-nettools.com/toolbox/proxy-test.php
```
