# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## SSH

**SSH** stands for ‘Secure SHell’. 
Today it's Client/Server-based de-facto standard for network remote access.
SSH Clients are included in almost all Linux versions out of the box 
(recent versions of Windows also include SSH Client).

> Read more on "How does the SSH protocol work" here: _https://www.ssh.com/academy/ssh/protocol#how-does-the-ssh-protocol-work_
> 
> It's important to understand that assymetric keys are **not used for traffic encryption/decrytion**. 
> Key-based access is used to generate symmetric one-time keys used for current session.

SSH allows:
* remote terminal access `ssh`
* remote file transfer `sftp`/`scp`
* automated single command execution


Examples:<br>
* `ssh student@10.1.10.1`
* `ssh -l student 10.1.10.1 date`
* `ssh student@10.1.10.1 ' echo "Hello Linux" > /tmp/hello' `
* `scp -r student@10.1.10.1:/etc/sysconfig /tmp`
* `scp  student@10.1.10.1:/bin/ls  student@10.1.10.2:/tmp`
* `sftp 10.1.10.1`


### SSH Windows Clients

Some free SSH/SCP/SFTP clients for Windows are (plus many commercial ones, like: SecureCRT,...):
* PuTTY (https://www.putty.org/) <br><br>
* Xshell/XFTP (https://www.netsarang.com/ru/free-for-home-school/) - Now is free for non-commercial use. <br><br>
* MobaXterm (https://mobaxterm.mobatek.net/) - Enhanced terminal for Windows with X11 server, tabbed SSH client, network tools and much more<br><br>
* Windows 10 (_from ver.1803_), OpenSSH Client has been implemented as a Windows 
feature _(if not can be added via “Optional features” (start typing in search “optional”…)_

### Using ssh without password (authenticating via public/private keypairs instead of password)

> Remember again that assymetric keys are **not used for traffic encryption/decrytion**. 
> Key-based access is used to generate symmetric one-time key used for current session
> * SSH Client puts it's public key in server's directory in `~/.ssh/authorized_keys`. When that client tries to connect, server generates a random message and asks to encrypt it with its private key.
> * SSH Server gets encrypted message from SSH Client and tries to decrypt it with Client's public key. 
> * If OK then it trusts that Client. SSH Server generates symmetric key and securely sends it to the Client.
> * This key can even be regenerated during a session upon mutual agreement.
>


Use `ssh-keygen` on your local system to generate public and private keys 
in SSH config directory: `~/.ssh`

* `ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_myserver`

Generally you can go forward with _Enter_'s. You may add security by specifying the passphrase, 
but it has to be entered every time the key is used for authentication.


> _Here we create keys of 4096 bits strength, which is stronger than the default 2048 bits._<br>
> _As a result we will get two new keys:_ 
> * `~/.ssh/id_myserver`
> * `~/.ssh/id_myserver.pub`<br><br>
>_KEEP IN SECRET `~/.ssh/id_myserver`_<br> 
>_it is your **private key**_<br>
>_`~/.ssh/id_myserver.pub` is your **public key**_ <br>
>_It could be copied to the location you want access without password._


Now securely copy your public key the `~/.ssh/id_myserver.pub` file to the
`~/.ssh` directory on the remote system. 

You can copy it in various ways like:

1.Using `ssh-copy-id`:<br>
* `ssh-copy-id -i ~/.ssh/id_myserver.pub user@host`

> _Non standard SSH port case can be handled this way: <br>
> ssh-copy-id -i ~/.ssh/id_myserver.pub '-p 2002 user@host' <br>
> (NOTE! The quotes are important)._

The above can be also done manually, like for the older SSH version cases, where `ssh-copy-id` is not present:

2.`cat ~/.ssh/id_myserver.pub | ssh user@host 'cat >> ~/.ssh/authorized_keys'`

Since you do it manually, ensure that .ssh exists and has proper permissions:

`ssh user@host  mkdir ~/.ssh`<br>
`ssh user@host chown -R user:user ~/.ssh/`<br>
`ssh user@host chmod 700 ~/.ssh`<br>

Also ensure that authorized_keys file has proper permissions: <br>
`ssh user@host chmod 600 ~/.ssh/authorized_keys`

Now you should be able to connect to the remote system via ssh without 
being prompted for a password.
This means you can either get a remote shell with ssh, 
or just run a single command remotely. 
Also you can use sftp and scp commands as well - all that without password.

```bash
ssh -i ~/.ssh/id_myserver 10.1.10.1
ssh -i ~/.ssh/id_myserver student@10.1.10.1 /bin/date
scp -i ~/.ssh/id_myserver student@10.1.10.1:/bin/ls  ./
sftp -i ~/.ssh/id_myserver 10.1.10.1
```

#### PRACTICE

1. Create keypair on one system and copy to another. 
Then try connecting without password.


2. Try connecting from Windows to Linux using available 
Windows clients.


3. Enter Windows `PowerShell`. 
Generate keypair and transfer to Linux to connect without password.
* `mkdir c:\Users\[username]\.ssh`
* `ssh-keygen -t rsa -b 4096 -f c:\Users\[username]/.ssh/id_myserver`
* `cd .ssh`
* `cat id_myserver.pub | ssh user@host 'cat >> ~/.ssh/authorized_keys'`

### TCP wrappers
TCP Wrapper is a host-based ACL system, used to filter network access to specific network services. 
It is old enough and not widely used anymore, but in most Linux versions 
TCP wrapper support is integrated into SSH, so it works for SSH and it's important to know how. 

> IMPORTANT! Different Linux distributions implement TCP Wrappers differently!
> More info:   
> `man 5 hosts_access` & `man 5 hosts_options`
>
> > TCP Wrappers were **deprecated** and **removed** from **CentOS/RHEL 8** (https://access.redhat.com/solutions/3906701)

Everything related to TCP wrapper is configured in two text files: 
* `/etc/hosts.allow`
* `/etc/hosts.deny`

Each time connection is open to SSH server (sshd) it 'pauses' the connection 
and looks to these files to determine if a connection should be granted or refused.

The order is:
It scans through the `hosts.allow` and `hosts.deny` files and stops when it finds an entry that matches the IP address of the connecting machine. These checks are made when connection attempts occur: 
* If the client IP-address matches `hosts.allow` file, the connection is **allowed** and nothing more is checked.
* Otherwise `hosts.deny` is checked and if client IP-address matches, the connection is **denied**.
* Finally, if the address is in neither file, the connection is **allowed**.

General syntax of `/etc/hosts.allow`, `/etc/hosts.deny` is:
```bash
service : host/network : option [: option] ...
```

It is not necessary (or even possible) to list every single address that may connect to your computer. The hosts.allow and hosts.deny files enable you to specify entire subnets and groups of addresses. You can even use the keyword ALL to specify all possible addresses. You can also restrict specific entries in these files so they only apply to specific network services 

```bash
# /etc/hosts.allow: list of hosts that are allowed to access the system.
#                   See the manual pages hosts_access(5) and hosts_options(5).
sshd: 127.0.0.1  10.1.10.1 
```

```bash
# /etc/hosts.deny: list of hosts that are _not_ allowed to access the system.
#                  See the manual pages hosts_access(5) and hosts_options(5).
sshd: ALL
```


### SSH HardeningTips

Changes below are to be done in the SSH Server configuration file:

`/etc/ssh/sshd_config`

#### Changing the listening port
Some say that obscurity is not security but that's not true. 
Any measure that makes attacking your system harder can be valid. 
One of the effective measures is **changing the SSH port**. 
> NOTE: If firewall (`firewalld`/`ufw`) is active, then its settings 
> need to be adjusted for the new SSH port too.

```bash
#Port 22
Port 5022
```

#### Turning IPv6 off
```bash
AddressFamily inet  #  inet - means IPv4 only AddressFamily
```

#### Listen to particular IP
```bash
#ListenAddress 0.0.0.0
#ListenAddress ::
ListenAddress 10.1.10.1
```

Restrict long login session and trun off root login
```bash
#LoginGraceTime 2m
LoginGraceTime 1m
#PermitRootLogin yes
PermitRootLogin no
```

Manage Public key  &  Password Authentication
```bash
#PubkeyAuthentication yes
PubkeyAuthentication no
PasswordAuthentication yes
```

You can require all logins use keys with 
```bash
PasswordAuthentication no
```

you can specify that only the root user must use a key with
```bash
PermitRootLogin without-password
```

Save the file and restart the SSH daemon:<br>
`systemctl restart sshd`

Check the port to ensure IPv6 is off now:<br>
`lsof -i  | grep ssh`

Now SSH is listening new port. 
You can try to connect:<br>
`ssh -p 5022 user@IP`

Also changing the port will bring the number of SSH brute-force attacks to zero.


### Limiting SSH access per user and per IP-address

OpenSSH provides the possibility to restrict access for specific user and/or specific IP addresses. 

SSH allows you to restrict users and groups by host or IP address. 
There are four different directives you can use in your `/etc/ssh/sshd_config` file (they are evaluated in this order):

```bash
DenyUsers
AllowUsers
DenyGroups
AllowGroups
```

The format for all of them will be the same - 
a space-separated list of users or group names, 
with optional host names.

#### PRACTICE

1. Try to limit any SSH users to access only from specific IP range (e.g. network 9.9.9.0/24) 
at the bottom of the file `/etc/ssh/sshd_config` add:
```bash
AllowUsers student@127.0.0.1
```

Save the file, restart SSH daemon, Now only users coming from network
10.10.10.0/24 should be able to login by ssh, any other source IP will always get “Wrong username or password”
Try connecting from localhost:

```bash
ssh -p 5502 stident@127.0.0.1
```
You should be able to connect, but below variants should not work:

```bash
ssh -p 5502 student@10.1.10.1
```

```bash
ssh -p 5502 root@127.0.0.1
```


2. Add per-user access config:

```bash
AllowUsers  student@127.0.0.1  *@10.1.10.*  specialuser
```

You should now be able to connect, with below variants:

```bash
ssh -p 5502 student@10.1.10.1
```

```bash
ssh -p 5502 root@127.0.0.1
```


### Restricting key-based SSH access to particular IP addresses

Rather than just storing the public keys of connecting users 
`~/.ssh/authorized_keys` file also allows to 
specify some additional configuration entries for each key:



> Options are comma-separated and are documented in the `man sshd`, 
> under the section `"AUTHORIZED_KEYS FILE FORMAT"`. 
> 
> Here are the most useful ones:
> 
> * **from="<hostname/ip>"**  _- Prepending from="*.example.com" to the key line would only allow public-key authenticated login if the connection was coming from some host with a reverse DNS of example.com. You can also put IP addresses in here. This is particularly useful for setting up automated processes through keys with null passphrases._
>
>> ```bash
>> * - Matches zero or more characters
>> ? - Matches exactly one character
>> ! - Negates the host pattern match
>> ```
>
> * **command="<command>"**  _- Means that once authenticated, the command specified is run, and the connection is closed. Again, this is useful in automated setups for running only a certain script on successful authentication, and nothing else._
> 
> 
> * **no-agent-forwarding**  _- Prevents the key user from forwarding authentication requests 
> to an SSH agent on their client, using the -A or ForwardAgent option to ssh._
>
> 
> * **no-port-forwarding** - _Prevents the key user from forwarding ports using -L and -R._
>
> 
> * **no-X11-forwarding**  - _Prevents the key user from forwarding X11 processes._
>
> 
> * **no-pty** - _Prevents the key user from being allocated a tty device at all (does not allow interactive login)_
>    

#### PRACTICE 1

Add to your following to the line of you public key, before "**ssh-rsa ...**" 
in `~/.ssh/authorized_keys` file: 

```bash
from="127.0.0.1,10.1.10.*",command="w" ssh-rsa ...
```

Now try connecting:  
* from allowed IP address

You will get `w` command output
* from other IP address

You will get password prompt

Such restriction can be useful for remote backups scripts, 
as it can ensure that your remote user can only execute the 
expected command - and not anything else.

> Another useful restrictions are, to disable use of agent-forwarding, port-forwarding, X11 and interactive logins.
> 
> >  ```bash
> > no-agent-forwarding,no-port-forwarding,no-X11-forwarding,no-pty ssh-rsa ...
> > ```
> 
> Recent OpenSSH versions >=7.2 have additional option `restrict` in the `authorized_keys`.
> It enables all restrictions, i.e. disable the above altogether. Also any future restriction capabilities are added to this option.
> 

#### PRACTICE 2

`command=` option is not supporting arguments.
Solution is in creating additional intermediate script like one below. 

1. Create file `/opt/checkssh` and make it executable.

```bash
#!/bin/bash
if [ -n "$SSH_ORIGINAL_COMMAND" ]; then
    if [[ "$SSH_ORIGINAL_COMMAND" =~ ^ls\  ]]; then
        echo "`/bin/date`: $SSH_ORIGINAL_COMMAND" >> $HOME/ssh-command-log
        exec $SSH_ORIGINAL_COMMAND
    else
        echo "`/bin/date`: DENIED $SSH_ORIGINAL_COMMAND" >> $HOME/ssh-command-log
    fi
fi
```
> NOTE: Allowed **ls** command in the above script specifically ends with `\ `, 
> which means, there should be a space after the command (for options/arguments):
> 
> `^ls\ `
> 

`chmod +x /opt/checkssh`

2. Modify `~/.ssh/authorized_keys` file:
```bash
from="127.0.0.1,10.1.10.*",command="/opt/checkssh" ssh-rsa ...
```

3. Try run 

`ssh student@127.0.0.1 ls /opt`

`ssh student@127.0.0.1 ls -l /tmp`

The same way other restricted commands can be specified (like `rsync`, discussed below).  


### Rsync

Rsync provides fast incremental file transfer. It synchronizes files and 
directories from one location to another. 
An important feature of rsync not found in most similar programs/protocols 
is that the mirroring takes place with only one transmission in each direction. 
rsync can copy or display directory contents and copy files, optionally using 
compression and recursion. 

```bash
rsync [OPTIONS] SOURCE DESTINATION
```

The most important `rsync` options are: 

```bash
-a, --archive		archive mode; same as -rlptgoD
  -r, --recursive          	recurse into directories
  -l, --links                 	copy symlinks as symlinks
  -p, --perms              	preserve permissions
  -t, --times                 	preserve modification times
  -g, --group               	preserve group
  -o, --owner              	 preserve owner (super-user only)
  -D, --devices --specials      preserve special files

--delete		delete files that don not exist on sender
-v, --verbose		increase verbosity
-u, --update		skip files that are newer on the receiver
-n, --dry-run           perform a trial run with no changes made
-z, --compress	compress file data during the transfer
```
#### PRACTICE Simple rsync Examples

1. Create some initial directories & files
```bash
mkdir /tmp/rs1 /tmp/rs2 ; \
fallocate -l 10K /tmp/rs1/f1 ; \
fallocate -l 10K /tmp/rs1/f2 ; \
fallocate -l 101M /tmp/rs1/f3 ; \
fallocate -l 122M /tmp/rs1/f4 
```
2. Run `rsync`
```bash
rsync -av --delete /tmp/rs1 127.0.0.1:/tmp/rs2
```

Now you have a full backup copy of `/tmp/rs1` directory 
on "remote" server's `127.0.0.1:/tmp/rs2` directory (inside it !)

3. Add a file and run `rsync` again
```bash
fallocate -l 5M /tmp/rs1/a5 ; \
rsync -av --delete /tmp/rs1 127.0.0.1:/tmp/rs2
```
You should see new file `/tmp/rs1/a5` added to "remote" copy.

4. Remove some file and run `rsync` again
```bash
rm /tmp/rs1/f3 ; \
rsync -av --delete /tmp/rs1 127.0.0.1:/tmp/rs2
```
You should see `f3` file removed from "remote" copy too.<br>
> NOTE: this is because of `--delete` option. 
> if you run `rsync` without this option it will only add files

5. Create new files in both dirs, but in second one first
   (we imitate we have newer file on destination)
```bash
fallocate -l 1M /tmp/rs1/f77 ; \
touch /tmp/rs2/rs1/f77 
```

6. Run `rsync` again <br> 
(NOTE: now we add `-u` option) <br>
and check if file `f77` file copied (it should not)
```bash
rsync -avu --delete /tmp/rs1 127.0.0.1:/tmp/rs2
ls -l /tmp/rs2/rs1
```

### SSH as a filesystem: sshfs
Using the FUSE project with `sshfs`, it's possible to mount a remote 
filesystem over SSH. 

CentOS package for `sshfs` is available from **EPEL** repository in CentOS 7. 
Make sure you have EPEL and type:<br>

```bash
yum -y install fuse-sshfs
```

> NOTE: For CentOS 8 you need to install from **powertools** repository (disabled by default)<br>
> `dnf --enablerepo=powertools -y install fuse-sshfs`
> <br><br>
> For Ubuntu package name is just `sshfs`
> `apt install sshfs` 


> IMPORTANT: once `sshfs` is installed, it can be used by any user. Users can mount remote directories somewhere in their home
 

Syntax is like:<br>
`sshfs user@remote.host:/somedir ~/localdir  -o reconnect`

> You should have key-based access configure for that user

#### PRACTICE
1. Mount remote directory via ssh link
```bash
mkdir /opt/sshfs
sshfs student@10.1.10.1:/tmp/rs1  /opt/sshfs -o reconnect
df -h
```
> To set other UID/GID on copied files we can add:<br>
> _-o uid=1000,gid=1000_<br>
> See `man sshfs` for more options

2. Unmount it
```bash
fusermount -u /opt/sshfs
df -h
```

> Automounting can be done by adding the appropriate line to `/etc/fstab`, 
> like:
> ```bash
> sshfs#user@remote.host:/somedir /somemydir fuse uid=1000,gid=1000 0 0
> ```

 


### Fail2ban – protect from brute-force attacks (SSH example)

If you pay attention to application logs for SSH service, you may see repeated, systematic login attempts that represent brute-force attacks by users and bots alike.

Fail2ban can mitigate this problem by creating rules that automatically alter your firewall configuration based on a predefined number of unsuccessful login attempts. This will allow your server to respond to illegitimate access attempts without intervention from you.

#### Install and configure Fail2ban

While Fail2ban is not available in the official CentOS package repository, 
it is packaged for the EPEL project. 
EPEL, standing for Extra Packages for Enterprise Linux, 
can be installed with a release package that is available from CentOS: 

`yum -y install epel-release`

Now we should be able to install the fail2ban package:  

`yum -y install fail2ban`

Once the installation has finished, use systemctl to enable the fail2ban service:

`systemctl enable fail2ban`

`systemctl start fail2ban`

Fail2ban service keeps its configuration files in the `/etc/fail2ban` directory. There, you can find a file with default values called `jail.conf`. 
Since this file may be overwritten by package upgrades, we shouldn't edit it in-place. Instead, we'll write a new file called `jail.local`. Any values defined in `jail.local` will override those in `jail.conf`.

#### PRACTICE

1. Create `/etc/fail2ban/jail.local`

```bash
[DEFAULT]
# Ban hosts for one hour
bantime = 3600
# Ban IP that does 'maxretry' failures within 'findtime' /600 seconds - 10 minutes/
findtime = 600
maxretry = 3
# Override /etc/fail2ban/jail.d/00-firewalld.conf:
banaction = iptables-multiport
[sshd]
enabled = true
```

2. Restart the fail2ban service using systemctl:

`systemctl restart fail2ban`

3. Check that the service is running:

`fail2ban-client status`

4. Get more detailed information about a specific jail:

`fail2ban-client status sshd`

5. Now try to login with ssh and enter wrong password 3 or more times. 

6. After that run again:

`fail2ban-client status sshd`

You should see your IP banned.

> Also the following command will show the current firewall rules enabled, 
where you will see you IP:
> 
> ```
> iptables -L -n
> iptables -S
> ```

7. Now remove your IP from ban list by:

`fail2ban-client set sshd unbanip <IP address>`

8. Create whitelist of your subnets with the following option added to `/etc/fail2ban/jail.local`:
```bash
ignoreip = 127.0.0.1/8 192.168.0.0/16 10.0.0.0/8
```

9. Restart the fail2ban:

`systemctl restart fail2ban`

10. Now again try to login with ssh and enter wrong password 3 or more times.
You should not be banned. Run again:

`fail2ban-client status sshd`

You should not see your IP address banned.
And you should be able to login.

<br><br>


For protecting other services you may see what kind of other filters are available:

`ls /etc/fail2ban/filter.d`

After any change remember to restart the fail2ban service:

`systemctl restart fail2ban`
