# Linux Administration and Networking Basics (level 2) <br /> Linux-ի կառավարում և ցանցային հիմունքներ (փուլ 2)

## SSH

**SSH** stands for ‘Secure SHell’. 
Today it's Client/Server-based de-facto standard for network remote access.
SSH Clients are included in almost all Linux versions out of the box 
(recent versions of Windows also include SSH Client).

> Read more on "How does the SSH protocol work" here: _https://www.ssh.com/academy/ssh/protocol#how-does-the-ssh-protocol-work_

SSH allows:
* remote terminal access `ssh`
* remote file transfer `sftp`/`scp`
* automated single command execution


Examples:<br>
* `ssh student@172.16.1.145`
* `ssh -l student 172.16.1.134 date`
* `ssh root@172.16.1.145 ' echo "Hello Linux" > /tmp/hello' `
* `scp -r student@172.16.1.83:/etc/sysconfig /tmp`
* `scp  student@172.16.1.134:/bin/ls  root@172.16.1.145:/tmp`
* `sftp 10.10.10.111`


### SSH Windows Clients

Some free SSH clients for Windows are (plus many commercial ones, like: SecureCRT,Xshell,...):
* PuTTY (http://www.putty.org/) <br><br>
* MobaXterm (http://mobaxterm.mobatek.net/) - Enhanced terminal for Windows with X11 server, tabbed SSH client, network tools and much more<br><br>
* Windows 10 (_from ver.1803_), OpenSSH Client has been implemented as a Windows 
feature _(if not can be added via “Optional features” (start typing in search “optional”…)_

### Using ssh without password (authenticating via public/private keypairs instead of password)
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
ssh -i ~/.ssh/id_myserver 10.6.0.111
ssh -i ~/.ssh/id_myserver student@10.6.0.111 /bin/date
scp -i ~/.ssh/id_myserver student@10.6.0.111:/bin/ls  ./
sftp -i ~/.ssh/id_myserver 10.6.0.111
```

#### PRACTICE

1. Create keypair on one system and copy to another. Then try connecting without password.


2. Try connecting from Windows to Linux using available Windows clients.


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
sshd: 199.170.177.  10.10.10.10 
```

```bash
# /etc/hosts.deny: list of hosts that are _not_ allowed to access the system.
#                  See the manual pages hosts_access(5) and hosts_options(5).
sshd: ALL
```

> IMPORTANT! Different Linux distributions implement TCP Wrappers differently!
> More info:   
> `man 5 hosts_access`
> `man 5 hosts_options`
> http://static.closedsrc.org/articles/dn-articles/hosts_allow.html <br>
> It is also possible to specify a facility using the severity option. The following example logs any SSH connection attempts by hosts from the example.com domain to the local7 facility with a priority of alert:<br>
> `sshd : .example.com : severity local7.alert`

Option fields also allow administrators to explicitly allow or deny hosts in a single rule by adding the allow or deny directive as the final option.

For instance, the following two rules allow SSH connections from client-1.example.com, but deny connections from client-2.example.com:

sshd : client-1.example.com : allow
sshd : client-2.example.com : deny


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
ListenAddress 172.16.0.1
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
PermitRootLogin without-password"
```

Save the file and restart the SSH daemon:<br>
`service sshd restart`

Check the port to ensure IPv6 is off now:<br>
`lsof -i  | grep ssh`

Now SSH is listening new port. 
You can try to connect:<br>
`ssh -p 5022 user@IP`

Also changing the port will bring the number of SSH brute-force attacks to zero.


### Limiting SSH access per user and per IP-address

OpenSSH provides the possibility to restrict access for specific user and/or specific IP addresses. 

SSH allows you to restrict users and groups by host or IP address. There are four different directives you can use in your `sshd_config` file (they are evaluated in this order):

```bash
DenyUsers
AllowUsers
DenyGroups
AllowGroups
```

The format for all of them will be the same - 
a space-separated list of users or group names, 
with optional host names. Here is an example:

1. To limit any SSH users to access only from specific IP range (e.g. network 9.9.9.0/24) at the bottom of the file /etc/ssh/sshd_config we can add:
```bash
AllowUsers *@9.9.9.*
```

Save the file, restart SSH daemon and this will take effect – only users coming from network
9.9.9.0/24 will be able to login by ssh, any other source IP will always get “Wrong username or password”

2. To limit some users to access from specific IPs but allow others 
3. from any address at the bottom of the file `/etc/ssh/sshd_config` we can add:
```bash
AllowUsers  student@1.2.3.4  test@5.6.7.*  specialuser
```

Thus we restrict user `student` to connect only from `1.2.3.4` IP address, 
user `test` to connect only from `5.6.7.0/24` subnet 
and `specialuser` can connect from anywhere.

### Restricting password-less SSH logins to particular IP addresses

Rather than just storing the public keys of connecting users 
`~/.ssh/authorized_keys` file also allows to 
specify some additional configuration entries:

Options are comma-separated and are documented in the `man sshd`, 
under the section "AUTHORIZED_KEYS FILE FORMAT". 

Here are the most useful ones:

* from="<hostname/ip>"  - Prepending from="*.example.com" to the key line would only allow public-key authenticated login if the connection was coming from some host with a reverse DNS of example.com. You can also put IP addresses in here. This is particularly useful for setting up automated processes through keys with null passphrases.

* command="<command>"  - Means that once authenticated, the command specified is run, and the connection is closed. Again, this is useful in automated setups for running only a certain script on successful authentication, and nothing else.

* no-agent-forwarding  - Prevents the key user from forwarding authentication requests 
to an SSH agent on their client, using the -A or ForwardAgent option to ssh.
* no-port-forwarding - Prevents the key user from forwarding ports using -L and -R.
* no-X11-forwarding  - Prevents the key user from forwarding X11 processes.
* no-pty - Prevents the key user from being allocated a tty device at all (does not allow interactive login)
    
As a good example of a secure login this is a good start:

```bash
from="127.0.0.1,10.10.10.*" ssh-rsa ...
```

Another possible restrictions are, to disable use of agent-forwarding, port-forwarding, etc. whilst still allowing interactive logins.

```bash
from="!127.0.0.1,10.0.0.?",no-agent-forwarding,no-port-forwarding,no-X11-forwarding ssh-rsa ...
```

```bash
* - Matches zero or more characters
? - Matches exactly one character
! - Negates the host pattern match
```

If you were using SSH for special-purpose logins you could restrict things further, by denying interactive login-shells and forcing the execution of a particular command:

```bash
no-pty,command="uptime" ssh-rsa ..
```

This is useful for remote backups carried out via rsync + ssh, as it can ensure that your remote user can only execute the expected command - and not anything else.

See more: http://www.unixlore.net/articles/five-minutes-to-even-more-secure-ssh.html
https://sanctum.geek.nz/arabesque/restricting-public-keys/


### SSH as a filesystem: sshfs
Using the FUSE project with `sshfs`, it's possible to mount a remote 
filesystem over SSH. CentOS package for `sshfs` is available from **EPEL** repository. 
Make sure you have EPEL and type:<br>
`yum -y install fuse-sshfs`

Once `sshfs` is installed, run it like:<br>
`sshfs user@remote.host:/somedir /somemydir  -o reconnect`

Example:
```bash
mkdir /media/usr-local
sshfs user@172.16.1.113:/usr/local  /media/usr-local -o reconnect
```
> To set other UID/GID on copied files we can add:<br>
> _-o uid=1000,gid=1000_<br>
> See `man sshfs` for more options

Unmounting can be done like:
`fusermount -u /media/usr-local`

Automounting can be done by adding the appropriate line to `/etc/fstab`, 
like:
`sshfs#user@remote.host:/somedir /somemydir fuse uid=1000,gid=1000 0 0`


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
-a, --archive		archive mode; same as -rlptgoD (no -H)
-r, --recursive          	recurse into directories
-l, --links                 	copy symlinks as symlinks
-p, --perms              	preserve permissions
-t, --times                 	preserve modification times
-g, --group               	preserve group
-o, --owner              	preserve owner (super-user only)
--devices               preserve device files (super-user only)
           --specials              preserve special files
-v, --verbose		increase verbosity
-u, --update		skip files that are newer on the receiver
--delete		delete files that don't exist on sender
-n, --dry-run              perform a trial run with no changes made
-z, --compress	compress file data during the transfer
```

Examples:
```bash
rsync -av --delete /etc/fonts 172.16.1.196:/tmp

rsync -av --exclude="fonts.conf" --delete 172.16.1.196:/tmp /etc/fonts 
```

#### PRACTICE Backup via Rsync
1. On Source Host:<br>
Ensure you have `rsync` package installed.
`yum -y install rsync`


2. On Destination Host<br>
* Ensure you have `rsync` package installed & server enabled and running.
```bash
yum -y install rsync
systemctl start rsyncd
systemctl enable rsyncd
```
* Prepare Backaup
```bash
mkdir /BACKUP
```

* Add following to `/etc/rsyncd.conf`:
```bash
# any name you like
[backup]
# destination directory for copy
path = /BACKUP
# hosts you allow to access
hosts allow = 172.16.1.0/24
hosts deny = *
list = true
uid = root
gid = root
read only = false
```

3. Execute rsync on Source Host like follows.
`rsync -avz --delete /home/ 172.16.1.196::backup`

4. Add to cron if you'd like to run regularly.


 
### Screen manager

Screen  is  a  remote terminal session manager that multiplexes a single connection between several windows (typically interactive shells). It provides the detaching/reattaching functionality. See man screen for more info.

`screen -RD`

Each process gets its own virtual window, and you can bounce between virtual windows interacting with each process. The processes managed by screen continue to run when their window is not active. Screen offers the ability to detach from a session and then attach to it at a later time. When detached from a session, the processes screen is managing continue to run. You can then re-attach to the session at a later time, and your terminals are still there, the way you left them.

Before actually running screen, it's important to understand how to interact with it. Screen sends all entered text to the current window, with the exception of the command character. The default command character is Ctrl-a (press the Ctrl and the a key at the same time).
The command character is used to notify screen that you'd like to control screen itself, rather than the application in the current window. The key pressed after the command character designates which screen command you would like to perform. Some of the more useful commands and their key bindings are shown in table below:
Keys bindings in screen:
```bash
Ctrl+a c		new window
Ctrl+a n		next window
Ctrl+a p	previous window
Ctrl+a d		detach screen from terminal.	Run 'screen -RD' to reattach
Ctrl+a A	set window title
Ctrl+a k	kill current window
```

#### PRACTICE

Start the screen
`screen`
(Detach from the screen with " Ctrl+a d")


`screen -ls`
There is a screen on:
        3209.pts-0.server (Dead ???)
        5345.pts-0.server (Detached)
Remove dead screens with 'screen -wipe'.
2 Sockets in /home/student/.screen.

`screen -wipe`
Remove dead screens

`screen -ls`
There is a screen on:
        5345.pts-0.server (Detached)
1 Sockets in /home/student/.screen.

`screen -rd  5345.pts-0.server`
Reattach a session  5345.pts-0.server 
(Detach again from the screen with "Ctrl+a d")
screen -x 5345
Attach  to  a  not  detached screen session. (Multi display mode)

`exit`
Close current screen session


> There is also `-d -m` switch combination to start  screen  in  "detached" mode, i.e. create a new session but don't attach to it. It’s useful for running scripts.

`.screenrc` is the per-user configuration file in your home directory, 
and /etc/screenrc is the system-wide configuration file that applies to all users.

Example of useful “.screenrc” config file:  
```bash
# Save this in ~/.screenrc
# Use bash
shell /bin/bash
autodetach on
# Big scrollback
defscrollback 5000
# No annoying startup message
startup_message off
# Display the status line at the bottom
hardstatus on
hardstatus alwayslastline
hardstatus string "%{.kW}%-w%{.bW}%t [%n]%{-}%+w %=%{..G} %H %{..Y} %Y/%m/%d %c"
# Setup screens
screen -t 'main' 0 bash # Make first screen - main
screen -t 'test'  1 bash # Make screen - test
screen -t 'workspace' 2 bash # Make screen for general work
# Switch to the workspace screen
select 2
```

> Another alternative programs are: `tmux` 
> http://habrahabr.ru/post/126996/



### Fail2ban – protect from brute-force attacks (SSH example)

If you pay attention to application logs for SSH service, you may see repeated, systematic login attempts that represent brute-force attacks by users and bots alike.

Fail2ban can mitigate this problem by creating rules that automatically alter your firewall configuration based on a predefined number of unsuccessful login attempts. This will allow your server to respond to illegitimate access attempts without intervention from you.

Install Fail2ban

While Fail2ban is not available in the official CentOS package repository, it is packaged for the EPEL project. EPEL, standing for Extra Packages for Enterprise Linux, can be installed with a release package that is available from CentOS:  yum -y install epel-release

Now we should be able to install the fail2ban package:  yum -y install fail2ban
Once the installation has finished, use systemctl to enable the fail2ban service:
systemctl enable fail2ban
Fail2ban service keeps its configuration files in the /etc/fail2ban directory. There, you can find a file with default values called jail.conf. Since this file may be overwritten by package upgrades, we shouldn't edit it in-place. Instead, we'll write a new file called jail.local. Any values defined in jail.local will override those in jail.conf.

`/etc/fail2ban/jail.local`

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

Restart the fail2ban service using systemctl:

`systemctl restart fail2ban`

In order to check that the service is running, we can use fail2ban-client:

`fail2ban-client status`

You can also get more detailed information about a specific jail:

`fail2ban-client status sshd`

Now try to login with ssh and enter wrong password several times. 
After that run again:  

`fail2ban-client status sshd`

You will see your IP banned.

Also the following command will show the current firewall rules enabled, where you will see you IP:

```
iptables -L -n
iptables -S
```
You can remove your IP from ban list by:

`fail2ban-client set sshd unbanip <IP address>`

You can create whitelist of you subnets with the following option added to `/etc/fail2ban/jail.local`:
```
ignoreip = 127.0.0.1/8 192.168.0.0/16 10.0.0.0/8
```

For protecting other services see what kind of other filters are available:
`ls /etc/fail2ban/filter.d`

For example add to `/etc/fail2ban/jail.local`
```bash
[nginx-botsearch]
enabled = true
[apache-botsearch]
enabled = true
```

Restart the fail2ban service using systemctl:
`systemctl restart fail2ban`



