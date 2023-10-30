# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)


## Cockpit 

Cockpit is a web-based graphical interface for administrating Linux servers.
It is available for most modern Linux versions.


### Install and enable Cockpit 


```bash
yum -y install cockpit ;\
systemctl enable --now cockpit.socket

```

You may need to add following for firewalld if it is enabled


```bash
firewall-cmd --permanent --zone=public --add-service=cockpit ;\
firewall-cmd --reload

```

That's it. 
Now try access with web browser:

`https://[ipaddress]:9090`

