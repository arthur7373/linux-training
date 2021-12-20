# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## Web Server (Apache, SSL, PHP, MySQL)


Ensure you have proper hostname set 

```bash
hostnamectl set-hostname lt01.am
```

Check:
```bash
hostnamectl ;\
hostname 
```

Install Apache and related stuff: 

For CentOS 8 first run this:  `dnf config-manager --set-enabled powertools`

```bash
yum -y install httpd mod_ssl openssl elinks lynx
```

Enable & start: 
```bash
systemctl enable --now httpd 
```

#### PRACTICE
* Now you have running Apache webserver, 
configure the system to use your local DNS server, 
where you already have `lt01.am` zone.
* Set current IP address of the server to `www.lt01.am` record in the DNS.
* If you did everything correct you should be able to open it locally, with `links www.lt01.am`
* Now try also `links lt01.am`. Did it open? Why? How to fix it?



### Apache configuration
Default website location directory is:  `/var/www/html` 

After installation Apache webserver is ready to use by default configuration.
But it would be good to fix some parameters as we do in examples below (to ensure more security hardening for production environment). More info at: http://httpd.apache.org. 

Apache configuration is stored in: `/etc/httpd`.  
Main config file is: `/etc/httpd/conf/httpd.conf` 
contains following lines:  `include conf.d/*.conf`
Which means include any `*.conf` files from `/etc/httpd/conf.d` 

Create a separate virtual host configuration file /etc/httpd/conf.d/lt01.am.conf:

```bash
cat > /etc/httpd/conf.d/lt01.am.conf 
```
> ```bash 
> <VirtualHost *:80> 
> ServerName lt01.am
> ServerAlias www.lt01.am
> DocumentRoot /var/www/lt01.am
> CustomLog /var/log/httpd/lt01.am-access.log combined
> ErrorLog /var/log/httpd/lt01.am-error.log
>  <Directory /var/www/lt01.am>
>       Options -Indexes
>      AllowOverride ALL
>  </Directory>
> </VirtualHost>
> ```

Create virtual host website directory:  
```bash
mkdir /var/www/lt01.am
```

Put some file there as ‘index.html’ to be displayed as main page:
```bash
cd /var/www/lt01.am ; wget ya.ru
```

Restart Apache:
```bash
systemctl restart httpd   
```

Check
```bash
links lt01.am
```

### Access Control with .htaccess (http://www.htaccess-guide.com/)

It is possible to restrict visitors by IP address. For this to be able 
on per-directory basis, without restarting Apache, we have in the above VirtualHost configuration following option `AllowOverride ALL`.
After that we can just create a `.htaccess` file in the directory we want to restrict access.

Let us create a restricted subdirectory `closed`
```bash
mkdir -p /var/www/lt01.am/closed/img ;\
cat << EOF1 > /var/www/lt01.am/closed/test.html
<h2> Hello Linux </h2>
EOF1
cat << EOF2 > /var/www/lt01.am/closed/.htaccess
order allow,deny 
allow from 127.
#allow from 10.
#allow from 192.168. 
options +indexes
EOF2
> ```

Now try opening it
```bash
links www.lt01.am/closed
```

To access it you need to uncomment `#allow from 10.` line in `.htaccess`
Remove hashtag and try again.

Now you should be able to access the directory, but you will see it's file list, because we have
`options +indexes` option. 

Try commenting it an open it again
```bash
links www.lt01.am/closed
```

You will not see the directory contents, but will be able to access files by the name:

```bash
links www.lt01.am/closed/test.html
```

Now we can create an index file 
```bash
cat << EOF1 > /var/www/lt01.am/closed/index.html
<h2> THIS IS INDEX OF CLOSED DIR </h2>
EOF1
```

Try to open it again
```bash
links www.lt01.am/closed
```

#### Error handling
We can add handling of HTTP protocols errors, like 404 - Not found
```bash
cat << EOF3 >> /var/www/lt01.am/closed/.htaccess
ErrorDocument 404 http://ping.eu
EOF3
```

Now if we try non existing URL, we will be redirected to `ping.eu`
```bash
links www.lt01.am/closed/123
```

#### Password protection

Let us create another subdirectory `secure` and make it password-protected
```bash
mkdir -p /var/www/lt01.am/secure/docs ;\
cat << EOF3 > /var/www/lt01.am/secure/index.html
<h1> Linux Training </h1>
EOF3
cat << EOF4 > /var/www/lt01.am/secure/.htaccess
AuthName "Member's Area Name"
AuthUserFile /etc/httpd/conf.d/.htpasswd
AuthType Basic
require valid-user
EOF4
```

Create user ‘test123’ and set the password 
```bash
htpasswd -c /etc/httpd/conf.d/.htpasswd  test123
```
> NOTE: `-c` is needed only once to create the new file
> To change password you will need to run:
> ```bash
> htpasswd /etc/httpd/conf.d/.htpasswd test123
> ```

Now try access this URL. You will require user/password
```bash
links www.lt01.am/secure/
```


### Installing PHP with database support

```bash
yum -y install php php-common php-gd php-xml php-mbstring php-mysqlnd php-gd
```
Restart Apache:
```bash
systemctl restart httpd
```

Create php script:   
```bash
echo '<?php phpinfo(); ?>' > /var/www/lt01.am/inf.php
```

Check: 
```bash
links http://www.lt01.am/inf.php
```
> (PHP Fine tuning can be done in config files: `/etc/php.ini`, `/etc/php.d/`)

Now we can create an PHP index file 
```bash
cat << EOF1 > /var/www/lt01.am/index.php
<?php echo "<h3>HI THIS PHP INDEX</h3>"; ?>
EOF1
```

Check: 
```bash
links http://www.lt01.am/
```


### Install MariaDB (MySQL)
```bash
yum -y install mariadb mariadb-server
```

Enable and Start MySQL:
```bash
systemctl enable --now mariadb  
```

Set  root password for MySQL:
```bash
mysqladmin -u root password 'new-password'
```

Check MySQL.  Create mysqltest.php
```bash
nano /var/www/lt01.am/mysqltest.php
```

> ```bash 
> <?php
> ini_set('display_errors', 1); 
> $dblocation = "localhost"; 
> $dbname = "mysql"; 	
> $dbuser = "root"; 	
> $dbpasswd = "new-password"; 
>  
> 
> $dbcnx = @mysql_connect($dblocation, $dbuser, $dbpasswd);
> if (!$dbcnx){
>     echo "<p>Error MySQL Server  not available</p>";
>     exit();
> }
> if (!@mysql_select_db($dbname,$dbcnx)){
>     echo "<p>Error database not available</p>";
>     exit();
> }
> $ver = mysql_query("SELECT VERSION()");
> if(!$ver){
>     echo "<p>Query Error</p>";
>     exit();
> }
> echo mysql_result($ver, 0);
> ?>
> ```

Check: 
```bash
links http://lt01.am/mysqltest.php
```

Examples of MySQL queries:
```bash
mysql -u root -p 
mysql> CREATE DATABASE tester;
mysql> SHOW DATABASES;
mysql> GRANT ALL PRIVILEGES ON tester.* TO tester@localhost IDENTIFIED BY 'tester_users_pass' WITH GRANT OPTION;
mysql> GRANT ALL PRIVILEGES ON tester.*  TO tester@'192.168.1.%' IDENTIFIED BY 'tester_users_pass' WITH GRANT OPTION;
mysql> flush privileges;
mysql> DROP DATABASE tester;
mysql -u tester -h 192.168.1.1 -p  
```

### Install and configure ‘mod_ssl’ for Apache

```bash
yum -y install mod_ssl openssl
```

Certificate request generation:

```bash
openssl req -nodes -newkey rsa:2048 -nodes -keyout lt01.am.key -out lt01.am.csr \
-subj "/C=AM/ST=Yerevan/L=Yerevan/O=AITC/OU=Linux Training/CN=lt01.am"
```

Self-signed key generation:
```bash
openssl x509 -req -days 3650 -in lt01.am.csr -signkey lt01.am.key -out lt01.am.crt
```

One-command variant:
```bash
openssl req -x509 -batch -nodes -days 3650 -newkey rsa:4096 -keyout lt01.am.key \ -out lt01.am.crt -subj "/C=AM/ST=Yerevan/L=Yerevan/O=AITC/OU=Linux Training/CN=lt01.am"
```


Put certificates at their place:
```bash
mv lt01.am.crt /etc/pki/tls/certs
mv lt01.am.key /etc/pki/tls/private/ 
```

### Create SSL Virtual Host

```bash
cat > /etc/httpd/conf.d/lt01.am-ssl.conf 
```

>```bash
> <VirtualHost *:443>
>         SSLEngine on
>        SSLCertificateFile /etc/pki/tls/certs/lt01.am.crt
>        SSLCertificateKeyFile /etc/pki/tls/private/lt01.am.key
>        <Directory /var/www/lt01.am >
>         AllowOverride All
>         </Directory>
>         DocumentRoot /var/www/lt01.am
>         ServerName lt01.am
> </VirtualHost>
> ```

Restart Apache: 
```bash
systemctl restart httpd 
```

Redirect 80 port to 443 (SSL)
```bash
/etc/httpd/conf.d/lt01.am.conf
```

> ```bash
> <VirtualHost *:80>
> ServerName www.lt01.am 
> ServerAlias lt01.am
> Redirect permanent  /  https://lt01.am/
> …
> </VirtualHost>
> ```

Restart Apache: 
```bash
systemctl restart httpd 
```


### Hardening Apache

```bash
ServerTokens Prod
ServerSignature Off 
```

Change

```bash
AddType application/x-httpd-php .php 
```
to

```bash
AddType application/x-httpd-php .php .php~
AddHandler php5-script .php .php~
AddType text/html .php .php~
```

Remove Indexes from Options 

Hide PHP version (X-Powered-By) in php.ini
(it could be located in different places /etc/php.ini, /etc/php5/apache2/php.ini, ) 
> expose_php = Off

> Reduce Timeout:		Timeout 45

Limit big requests:	
> LimitRequestBody 1048576


