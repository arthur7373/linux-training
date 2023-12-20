# Linux Administration and Networking Basics (level 2) Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## Managing Periodic processes (cron)
You can configure Linux to automatically run some scheduled processes (also named tasks or jobs).
**Cron** is a service that enables you to schedule periodically running a task/job. A cron job is only executed if the system is running on the scheduled time.

There are also other similiar tools: 
- at is used to schedule a one-time job, to run once at a specific time.
- anacron differs from cron mainly in that:

1. if the system is not running at the scheduled time, job is postponed until the system is running 
2. anacron job can run once per day at most.

![img_1.png](../images/img_1.png)

```bash
$ crontab -l 
$ crontab -e 
```
~~crontab -r~~

### PRACTICE

1. Create directory /tmp/task1
```bash
mkdir /tmp/task1
```
2. Create files of different sizes in directory /tmp/task1
   (_to create example files of desired sizes we use different ways just for fun_)
```bash
fallocate -l 10K /tmp/task1/f1
fallocate -l 10K /tmp/task1/f2
fallocate -l 101M /tmp/task1/f3
fallocate -l 122M /tmp/task1/f4
truncate -s 15K /tmp/task1/a2
head -c 10K /dev/zero > /tmp/task1/a1
```
3. Now write command to find files
   1. larger than 10k size 
   2. and starting with "f" 
   
   and remove them


4. Create cronjob to run once per 2 minutes to do that job

## Managing System Logs (rsyslog)

**Log files** contain event messages from the kernel, services, applications. 

Log files can be very useful when trying to troubleshoot a problem 
with the system or some process such as status of some service 
or when looking for unauthorized login attempts to the system. 

There are many applications that manage their own logging for themselves
(such as Apache, Nginx, MySQL, etc).

But Linux operating system itself (kernel) and basic processes use single  
logging solution called **syslog**. **Syslog** is general name of soltion
implemented in most Linux versions by means of package called `rsyslog` (reliable syslog).
> _There are alternatives to `rsyslog`, like `syslog-ng`, but they are rarely
installed by default._

We can enable rsyslog daemon to start automatically on every reboot
and start it now with following commands:
```
systemctl enable rsyslog
systemctl start rsyslog
```
or
```
chkconfig rsyslog on
service rsyslog start
```
The main configuration file for rsyslog is `/etc/rsyslog.conf`. 
It consists of modules, global directives, rules or comments.
(read more at: https://access.redhat.com/site/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/ch-Viewing_and_Managing_Log_Files.html)

**Rsyslog** offers various ways how to filter syslog messages according to various properties. 
The most used and well-known way to filter syslog messages is to use the facility/priority-based filters which filter syslog messages based on two conditions: facility and priority.

**Rsyslog** can be configured to log different events to different places. Events can be selected based on 
the service that encountered the event (‘facility’) and/or severity (‘priority’). Messages can go to files, to the system console, or to a centralised rsyslog server running on another machine.

Basic rsyslog configuration in `/etc/rsyslog.conf` looks like:

```
facility.priority           destination
```

The **facility** specifies the _category_ of syslog message. 
It can be one of: 
`auth`, `authpriv`, `cron`, `daemon`, `kern`, `lpr`, `mail`, `news`, `syslog`, `user`, `uucp`, `local0 - local7`. 

The **priority** specifies a _priority_ of syslog message,
i.e a severity threshold beyond which messages will be logged.
It canbe one of (from lowest to highest): 
`debug`, `info`, `notice`, `warning`, `err`, `crit`, `alert`, `emerg`.

The **destination** indicates where messages selected by the facility and level will be sent: 
normally the name of a **log file** (under **/var/log**), /dev/console to send messages to the **system console or DNS name of the another server**, where to send the log (that server must have also syslogd process running).

```bash
# Log anything of level info or higher.
# Don't log private authentication messages, mail and cron
*.info;mail.none;authpriv.none;cron.none      /var/log/messages
# Log all the private authentication messages here
authpriv.*                                    /var/log/secure
# Log all the mail messages here
mail.*                                        -/var/log/maillog
```

>>  _Prepending dash in `destination` means to not synchronize the log file to disk
>>  every time there is a write, if synchronization behavior is on by default.
>>  In `rsyslog v3 and higher` default behavior is **not sync**, and it's possible 
>>  to change this by specifying "$ActionFileEnableSync on/off".
>>  More info: https://www.rsyslog.com/doc/v8-stable/compatibility/v3compatibility.html#output-file-syncing_

Realtime logs examining can be done like: 
```bash
tail -f /var/log/secure
```

Although main config file for **rsyslog** is `/etc/rsyslog.conf`, it also includes `*.conf` files from `/etc/rsyslog.d/` directory.
```bash
# Include all config files in /etc/rsyslog.d/
$IncludeConfig /etc/rsyslog.d/*.conf
```
On some systems the basic default functionality is moved from `/etc/rsyslog.conf` to the file:
`/etc/rsyslog.d/50-default.conf`


### Logrotate Log Rotation
If not controlled log files may grow without bound until you run out of disk space.  
The solution is to use log rotation: a scheme whereby existing log files are periodically
renamed and ultimately deleted. But rsyslog continues to write messages 
into the file with the ‘correct’ (same) name. 

Most Linux systems come with a program called logrotate, which should be run daily by cron (`/etc/cron.daily/logrotate`).
logrotate can be configured with `/etc/logrotate.conf` to perform rotation on any or all log files. 

Although main config file for logrotate is `/etc/logrotate.conf`, it also includes all files from /etc/logrotate.d/ directory.
This way logrotate rotates files not only for `rsyslogd`, but for many other services.
You can configure each logfile how often it should be rotated and how many old logs are kept.

### Logger  Utility
logger command is a shell command interface to the syslog system log module. 
It makes or writes one line entries in the system log file from the command line.

```bash
logger  -p daemon.info "TESTING DAEMON"
tail -1 /var/log/messages

logger  -p authpriv.info "TESTING AUTHPRIV"
tail -1 /var/log/secure
```

**Rsyslog** gives possibility to create custom logs unrelated to _facility_ settings.
For example, create separate config file:

```bash
cat > /etc/rsyslog.d/testing.conf 
:msg, contains, "TESTING" /var/log/testing.log
```

Restart rsyslog:
```bash
systemctl restart rsyslog
```
Check:
```bash
logger  -p local3.info "TESTING LOCAL3"
logger  -p local5.info "TESTING LOCAL5"
logger  -p mail.info "TESTING MAIL"
logger  -p auth.info "TESTING AUTH"
cat /var/log/testing.log
```
### Centralized Logging Server Configuration
**RSyslog** can be configured to log data from remote servers. This can help the Linux admin to have a multiple server logs into one single place. The Linux admin not required to login in to each servers for checking the logs, he can just login into the centralized server and start do the logs monitoring.

![img.png](../images/img.png)

> To remind: Linux labels (auth, cron, ftp, lpr, authpriv, news, mail, syslog, etc ,..) the log messages to indicate the type of software that generated the messages with severity (Alert, critical, Warning, Notice, info, etc ,..). You can find more information on Message Labels (http://en.wikipedia.org/wiki/Syslog#Facility_levels) and Severity Levels (http://en.wikipedia.org/wiki/Syslog#Severity_levels)

**Server setup:**
In `/etc/rsyslog.conf` uncomment the following lines:


```bash
# Provides UDP syslog reception
$ModLoad imudp
$UDPServerRun 514
 
# Provides TCP syslog reception
$ModLoad imtcp
$InputTCPServerRun 514
```
and restart the rsyslog service:
```bash
systemctl restart rsyslog
```
or 
```bash
service rsyslog restart
```
Verify the syslog server listening:
```bash
 netstat -antup | grep 514
```
or
```bash
 ss -antup | grep 514
```

### Firewall Port opening (optional):
Mostly all the production environment are protected by hardware firewall, ask them to open the TCP & UDP 514.
If you have IP tables enabled, run the following command on server in order to accept incoming traffic on UDP / TCP port 514.

Check status:
```bash
systemctl is-enabled firewalld
```

```bash
firewall-cmd --permanent --zone=public --add-port=514/tcp
firewall-cmd --permanent --zone=public --add-port=514/udp
firewall-cmd --reload
```
or disable the firewall:
```bash
systemctl stop firewalld
systemctl disable firewalld
```

#### Allow SELinux 
If you have SELinux enabled on your system, use following command to enable rsyslog traffic on port 514:
```bash
semanage -a -t syslogd_port_t -p udp 514
```
You can verify the port opening by issuing the following command from the client.
```bash
telnet 172.16.1.58 514
```


Client setup:


Add new config `/etc/rsyslog.d/nettest.conf`: 
```bash
cat > /etc/rsyslog.d/nettest.conf
*.* @@192.168.2.79:514
```
and restart the rsyslog service:
```bash
systemctl restart rsyslog
```
Now all message logs are additionally sent to the central server.


#### Testing

Monitor the activity from the log server, open the message log.

On server:
```bash
tail -f /var/log/messages
```

On client:
```bash
logger  -p daemon.info "TESTING REMOTE LOGGING"
```
By this way you can monitor the other logs such as secure, mail, cron logs etc.


