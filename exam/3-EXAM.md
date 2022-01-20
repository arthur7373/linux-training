# Linux Network Server (level 3) EXAM

## Install following service on separate VM and present as your EXAM result

### Before you start with exam task on new VM 
Since `SELinux` and `firewalld` may cause problems, 
turn them off for this exam task.

Turn off firewalld & SELinux
```bash
systemctl disable --now firewalld ;\
setenforce 0
```
Ensure you have proper hostname set 

```bash
hostnamectl set-hostname linuxexam.am
```

### Install and configure BIND DNS Server
```bash
yum -y install bind-chroot bind-utils
```

Non-chroot BIND version (if any) should be stopped and disabled	
```bash
systemctl disable --now named
```

Setup chroot environment for BIND:
```bash
/usr/libexec/setup-named-chroot.sh /var/named/chroot on
```

Enable and start service
```bash
systemctl enable --now named-chroot
```

Make few changes in default config.
Default config listens only to `127.0.0.1` 
and serves only queries from `localhost`

Open `/var/named/chroot/etc/named.conf`

find section `options { … }` and change:
> listen-on port 53 { 127.0.0.1; };
to	
> listen-on port 53 { any; }; 

also change:
> allow-query     { localhost; };
to
> allow-query     { any; }; 

Create `linuxexam.am` domain zone

Create master zone config file `/var/named/chroot/etc/named/linuxexam.am.zone`

```bash
cat << EOF1 >> /var/named/chroot/etc/named/linuxexam.am.zone
 zone "linuxexam.am." IN {
         type master;
         file "linuxexam.am.db";
 };
EOF1
 ```

Include it in main config file `/var/named/chroot/etc/named.conf`

```bash
cat << EOF1 >> /var/named/chroot/etc/named.conf
include "/etc/named/linuxexam.am.zone";
EOF1
```


Create master zone data file 
`/var/named/chroot/var/named/linuxexam.am.db`

```bash
cat << "EOF1" > /var/named/chroot/var/named/linuxexam.am.db
$TTL 1H
@       SOA     ns.linuxexam.am.     dns.linuxexam.am. ( 2021121100
                            3H ; refresh
                            1H ; retry
                            1W ; expire
                            1H ) ; minimum
                            NS       ns.linuxexam.am.
                            MX       0 mail
                            A       10.10.1.1
www                        A       10.10.1.1
ns                         A       10.10.1.2
mail                       A       10.10.1.3
EOF1
 ```

> NOTE! Remember to put your server's IP address instead of `10.10.1.1` in both places

Restart the service

```bash
systemctl restart named-chroot
```

Check

```bash
host -t a linuxexam.am 127.0.0.1 >> /tmp/exam-dns.out
```

Now you should have local DNS server, 
with properly configured zone `linuxexam.am` 
and A records `linuxexam.am` & `www.linuxexam.am` 
pointing to your server's IP address. 

> DON'T FORGET! Configure your `/etc/resolv.conf` to refer `nameserver 127.0.0.1`



### Install and configure Apache Web Server

Install Apache and related stuff: 

For CentOS 8 first run this:  `dnf config-manager --set-enabled powertools`

```bash
yum -y install httpd mod_ssl openssl elinks lynx
```

Change Apache default port from `80` to `8080`. 

Edit below lines in `/etc/httpd/conf/httpd.conf`
```bash
#Listen 80
Listen 8080
```

Enable & start: 
```bash
systemctl enable --now httpd 
```

Create a separate virtual host configuration for `linuxexam.am` website
/etc/httpd/conf.d/linuxexam.am.conf:

```bash
cat << "EOF1" > /etc/httpd/conf.d/linuxexam.am.conf 
<VirtualHost *:8080> 
ServerName linuxexam.am
ServerAlias www.linuxexam.am
DocumentRoot /var/www/linuxexam.am
CustomLog /var/log/httpd/linuxexam.am-access.log combined
ErrorLog /var/log/httpd/linuxexam.am-error.log
<Directory /var/www/linuxexam.am>
       Options -Indexes
      AllowOverride ALL
  </Directory>
 </VirtualHost>
EOF1
> ```

Create virtual host website directory:  
```bash
mkdir /var/www/linuxexam.am
```

Put some index page there:
```bash
cat << EOF1 > /var/www/linuxexam.am/index.html
HI this is APACHE page
EOF1
```

Restart Apache:
```bash
systemctl restart httpd   
```

Check that Apache listens port 8080:

```bash
netstat -nlpt | grep httpd
```

Check
```bash
links http://linuxexam.am:8080/
curl -v  http://www.linuxexam.am:8080/ | grep APACHE
```

### Install and configure NGINX Web Server

Install Nginx:  
```bash
yum -y install nginx
```

Reconfigure Nginx to listen port `8088` instead `80`

In file `/etc/nginx/nginx.conf` change the following lines:
```bash
#        listen       80 default_server;
#        listen       [::]:80 default_server;
listen       8088 default_server;
listen       [::]:8088 default_server;
```
 
Create file `/etc/nginx/conf.d/linuxexam.am.conf` with another port:

```bash
cat << EOF1 > /etc/nginx/conf.d/linuxexam.am.conf
server {
listen *:8088;
access_log /var/log/nginx/linuxexam.am-access.log;
error_log /var/log/nginx/linuxexam.am-error.log;
server_name linuxexam.am www.linuxexam.am;
# Redirect to backend
location / {
root /var/www/linuxexam.am-nginx;
}
}
EOF1
```

Create new directory for Nginx virtual host:

```bash
mkdir /var/www/linuxexam.am-nginx
```

Put some index page there:
```bash
cat << EOF1 > /var/www/linuxexam.am-nginx/index.html
HI this is NGINX page
EOF1
```
Enable & start Nginx: 
```bash
systemctl enable --now nginx
```

Check that Nginx listens port 8088:

```bash
netstat -nlpt | grep nginx 
```

Check
```bash
links http://linuxexam.am:8088
curl -v  http://www.linuxexam.am:8088/ | grep NGINX
```

### Install and configure HAProxy

This example is based on the environment like follows.
```bash
--------+---------------------+----------------------+------------
        |                     |                      |
        |[IP address]:80      |[IP address]:8080     |[IP address]:8088
+-------+--------+   +--------+---------+   +--------+---------+
|   [ linuxexam.am ]  |   | [ linuxexam.am:8080 ] |   | [ linuxexam.am:8088 ] |
|     HAProxy    |   | Apache Server #1 |   |  Nginx Server#2  |
+----------------+   +------------------+   +------------------+

```

Install HAProxy to configure Load Balancing Server.

```bash
yum -y install haproxy
```

Create simple configuration.

```bash
cat << EOF1 > /etc/haproxy/haproxy.cfg
frontend http-in
    bind *:80
    default_backend    backend_servers
    option             forwardfor
backend backend_servers
    balance            roundrobin
    server             node01 127.0.0.1:8080 check
    server             node02 127.0.0.1:8088 check
EOF1
```

Enable and start HAProxy
```bash
systemctl enable --now haproxy
```

Check the status of all related services:
```bash
netstat -nlpt | grep -E '(haproxy|http|nginx)' >> /tmp/exam-webservers.out
```

Now you connect to `linuxexam.am` several times. 
You should see Apache and Nginx pages in rotation

```bash
curl -s http://linuxexam.am/ | grep -E '(APACHE|NGINX)' >> /tmp/exam-haproxy.out ; \
curl -s http://linuxexam.am/ | grep -E '(APACHE|NGINX)' >> /tmp/exam-haproxy.out ; \
curl -s http://linuxexam.am/ | grep -E '(APACHE|NGINX)' >> /tmp/exam-haproxy.out
cat  /tmp/exam-haproxy.out
```

### Install and configure Squid 

Install Squid:

```bash
yum -y install squid
```

Enable and start Squid
```bash
systemctl enable --now squid
```

Check.
In one terminal open this:  
```bash
tail -f /var/log/squid/access.log
```
In another try opening this URL:

```bash
curl -s -x http://127.0.0.1:3128 http://all-nettools.com/toolbox/proxy-test.php | grep detected ;\
curl -s -x http://127.0.0.1:3128 http://all-nettools.com/toolbox/proxy-test.php | grep detected >> /tmp/exam-squid.out

```

Now hide HTTP headers that reveal you are behind the proxy

```bash
cat <<EOF4  >> /etc/squid/squid.conf
request_header_access Via deny all
request_header_access X-Forwarded-For deny all
request_header_access From deny all
request_header_access Link deny all
request_header_access Server deny all
EOF4
```
Reload Squid
```bash
systemctl reload squid
```

Check
```bash
curl -s -x http://127.0.0.1:3128 http://all-nettools.com/toolbox/proxy-test.php | grep "not detected" ;\
curl -s -x http://127.0.0.1:3128 http://all-nettools.com/toolbox/proxy-test.php | grep "not detected" >> /tmp/exam-squid.out
```

### Install and configure Mail server (Postfix, Dovecot)

Install Postfix.

```bash
yum -y install postfix 
```

Configure Postfix

```bash	
# goto line 95: uncomment and specify hostname
myhostname = linuxexam.am
# goto line 102: uncomment and specify domain name
mydomain = linuxexam.am
# goto line 118: uncomment
myorigin = $mydomain
# goto line 135: change
inet_interfaces = all
# goto line 183: add
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
# goto line 283: uncomment and specify your local network
mynetworks = 127.0.0.0/8, 10.0.0.0/24
# goto line 438: uncomment (use Maildir)
home_mailbox = Maildir/

# add following to the end of file

# SMTP-Auth setting
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
smtpd_sasl_local_domain = $myhostname
smtpd_recipient_restrictions = permit_mynetworks, permit_auth_destination, permit_sasl_authenticated, reject
```

Enable and start Postfix:
```bash
systemctl enable --now postfix
```

Install Dovecot 
```bash
yum -y install dovecot
```

Configure Dovecot to provide SASL (Simple Authentication and Security Layer) capability to Postfix

```bash
nano +30 /etc/dovecot/dovecot.conf
```

```bash
# add following after line 30
listen = *
```

```bash
nano +10 /etc/dovecot/conf.d/10-auth.conf
```

```bash
# add following after line 10
disable_plaintext_auth = no
# go to line 100: and change as follows
auth_mechanisms = plain login
```

```bash
nano +30 /etc/dovecot/conf.d/10-mail.conf
```
```bash
# add following after line 30
mail_location = maildir:~/Maildir
```

```bash
nano +107 /etc/dovecot/conf.d/10-master.conf
```

```bash
# uncomment line 107-109 and add some lines as follows
# Postfix smtp-auth
  unix_listener /var/spool/postfix/private/auth {
    mode = 0666
    user = postfix
    group = postfix
  }
```

```bash
nano +8 /etc/dovecot/conf.d/10-ssl.conf
```

```bash
# change line 8 as follows (means SSL is not required)
ssl = yes
```

Enable and start Dovecot:
```bash
systemctl enable --now dovecot
```

Install simple terminal mail client program (& telnet if needed)
```bash
yum -y install mailx telnet
```

Set environment variables to use Maildir:
```bash
echo 'export MAIL=$HOME/Maildir' >> /etc/profile.d/mail.sh
```

Add a user `exam`
```bash
useradd exam ;\
passwd exam
```

```bash
telnet linuxexam.am 25
> Trying 192.168.1.1...
> Connected to linuxexam.am.
> Escape character is '^]'.
> 220 linuxexam.am SMTP on Fri, 3 Aug 2001 10:38:06 +0400
helo lo
> 250 yahoo.com Hello root@linuxexam.am [192.168.2.200], pleased to meet you
mail from: user@yahoo.com
> 250 user@yahoo.com... Sender ok
rcpt to: exam@linuxexam.am     
> 250 exam@linuxexam.am... Recipient ok
data
> 354 Enter mail, end with "." on a line by itself
From: "TEST" <test@mail.com>
To: "TEST" <test@mail.com>
Subject: Test message
Date: Mon, 02 Feb 1991 13:00:57 +0400

Hello, This is a test message.

Yours truly,
Administrator
.
> 250 KAA24894 Message accepted for delivery
quit
```

Try sending mail via terminal `mail` command 
```bash
mail exam@linuxexam.am
```

Switch to `exam` user and check mail
(type `q` to exit  `mail` program)
```bash
su - exam
mail
```

Install `pflogsumm` which is the Postfix Log reporting tool.

```bash
yum -y install postfix-perl-scripts
```

Generate mail log summary for today
```bash
perl /usr/sbin/pflogsumm -d today /var/log/maillog >> /tmp/exam-mail.out ;\
less /tmp/exam-mail.out
```
