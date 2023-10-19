# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## Samba - network resources sharing

Samba is a free & open-source implementation of **network resources sharing** service for integrating servers and desktops running Linux or Unix in environments with Microsoft’s Active Directory directory service. 
Samba can act either as a primary domain controller or as a member. I can just be as single server to share network resources: files, printers, etc.

Samba uses the frequently used client/server protocols SMB (Server Message Block) or nowadays CIFS (Common Internet File System). 
The latter is an open variant of SMB. If applications are compatible with SMB or CIFS, they can communicate with the Samba Server.

Samba’s SMB/CIFS terminal client in Linux is called `smbclient`. 

### Install Samba & start services

```bash
yum install -y samba samba-client ;\
service nmb start ;\
service smb start
```
> Instead of old `service` command we can use modern `systemctl` as well, but this shows that `service` is still working.


### Register existing Linux user in Samba system

Type some password that will be used for Samba access only.

```bash
smbpasswd -a student
```

### Create simple configuration

```bash
mv /etc/samba/smb.conf{,.backup} ;\
cat << "EOF1" > /etc/samba/smb.conf
[global]
workgroup = MYGROUP
server string = SAMBA SHARING
log file = /var/log/samba/log.%m
max log size = 50
wins support = Yes

security = user
passdb backend = tdbsam

[homes]
comment = Home Directories
browseable = no
writable = yes

service nmb restart ;\
service smb restart
EOF1

```

Now we have one shared resource:
* homes - user writable home directory (each authenticated user will see here own home directory)

Let's access it via Windows

* Open the "File Explorer" and on the left-panel right-click on "This PC".
* Select "Add a network location", Then from menu select: 
* _Choose custom network location_
* _Next_
* In "**Internet or network address**" type:
`\\<ip address>\homes`
* Either go with `Next` or click on `Browse`

After entering credentials of user `student`, you should be able to see `student`'s home directory.


Now let's add new share:
* docs - public readonly resource

```bash
mkdir -p /srv/samba/docs/testdir ;\
echo "Test text"  >> /srv/docs/testdocument.txt ;\
cat << "EOF1" >> /etc/samba/smb.conf
[docs]
path = /srv/samba/docs
read only = yes
guest ok = yes
browsable = yes
EOF1
service nmb restart ;\
service smb restart
```

Try access the share again. You can see `docs` as well, but you will not be able to change anything there.

Now let's add another new share:
* tmp - public writable /tmp directory


```bash
cat << "EOF1" >> /etc/samba/smb.conf
[tmp]
path = /tmp
read only = No
EOF1

service nmb restart ;\
service smb restart
```

Try access the share again.
You should now see 3 shared resources:

* homes - user writable home directory (each authenticated user will see here own home directory)
* docs - public readonly resource
* tmp - public writable /tmp directory


Now let's create new Samba user `smbtest`

```bash
mkdir -p /srv/samba/smbtest ;\
echo "Test text 2" > /srv/samba/smbtest/testfile2.txt ;\
useradd -M -d /srv/samba/smbtest -s /usr/sbin/nologin smbtest ;\
smbpasswd -a smbtest ;\
service nmb restart ;\
service smb restart
```

Now try accessing with that new user `smbtest`

```bash
smbclient //127.0.0.1/smbtest -U smbtest
```

After connecting you shoule see `smb: \>` prompt.
Try some commands. For example 
```bash
ls
```

Or `more` to see the test file we created

```bash
more testfile2.txt
```

You can quit **smbclient** with `q` command.

### Mount Samba share

For mounting Samba share to Linux, you must install the `cifs-utils` package.

```bash
yum -y install cifs-utils
```

```bash
mount -t cifs -o username=smbtest,pass=123456 //127.0.0.1/smbtest /mnt/
```

Now you have mounted the Samba share locally to `/mnt`. 
Check:

```bash
df -h | grep '/mnt'
```

The same way it can be mounted remotely as well.



