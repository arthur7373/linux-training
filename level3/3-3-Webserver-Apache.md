# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## Web Server (Apache)


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
```bash
dnf config-manager --set-enabled powertools
dnf -y install httpd mod_ssl elinks lynx
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

