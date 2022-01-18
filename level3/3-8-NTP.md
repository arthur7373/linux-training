# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## NTP : Configure NTP Server
_(based on https://www.server-world.info/en/note?os=CentOS_8&p=ntp&f=1)_

### Time configuration

There are three main command line tools that can be used to configure and display information about the system date and time:

* `date` traditional command.
* `timedatectl` utility, which is new in Red Hat Enterprise Linux and is part of `systemd`.
* `hwclock` utility for accessing the hardware clock.

Set proper timezone
```bash
timedatectl list-timezones | grep Yerevan
```

```bash
timedatectl set-timezone Asia/Yerevan
```

### Install Chrony to Configure NTP Server for Time Synchronization.

Install Chrony

```bash
dnf -y install chrony
```

Configure Chrony, edit `/etc/chrony.conf`
```bash
# line 3: change servers to synchronize (replace to your own timezone NTP server)
# need NTP server itself to sync time with other NTP server
#pool 2.centos.pool.ntp.org iburst
pool am.pool.ntp.org iburst
# line 24: add network range to allow to receive time synchronization requests from NTP Clients
# specify your local network and so on
# if not specified, only localhost is allowed
allow 192.168.0.0/16
```

Start Chrony
```bash
systemctl enable --now chronyd
```
