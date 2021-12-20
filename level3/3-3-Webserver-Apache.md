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

For CentOS 8 first run this:<br> `dnf config-manager --set-enabled powertools`

```bash
yum -y install httpd mod_ssl openssl elinks lynx
```

Enable & start: 
```bash
systemctl enable --now httpd 
```

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
> ```bash <VirtualHost *:80> 
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

Restrict visitors by IP address

```bash
cat > /var/www/lt01.am/.htaccess
```
> ```bash
> order allow,deny
> allow from 127.
> allow from 10.
> allow from 192.168. 
> ```

Error documents
```bash
cat >> /var/www/lt01.am/.htaccess
```
> ```bash
> ErrorDocument 404 http://ya.ru
> ```

Password protection

```bash
cat >> /var/www/lt01.am/.htaccess
```
> ```bash
> AuthName "Member's Area Name"
> AuthUserFile /etc/httpd/conf.d/.htpasswd
> AuthType Basic
> require valid-user
> ```

Create user ‘test123’ and set the password (-c is needed only once to create the new file)
```bash
htpasswd -c /etc/httpd/conf.d/.htpasswd  test123
```

To change password use:
```bash
htpasswd /etc/httpd/conf.d/.htpasswd  test123
```

### Installing PHP with database support

```bash
yum -y install php php-common php-gd php-xml php-mbstring php-mcrypt 
yum -y install php-pecl-memcache php-mysql php-gd ImageMagick-devel
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
links http://lt01.am/inf.php
```
> (PHP Fine tuning can be done in config files: `/etc/php.ini`, `/etc/php.d/`)

### Install MariaDB (MySQL)
```bash
yum -y install mariadb mariadb-server 
```

Enable MySQL:
```bash
systemctl enable mariadb  
```
Start MySQL:
```bash
systemctl start mariadb
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


