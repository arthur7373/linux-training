# Linux Network Server (level 3) <br /> Linux ցանցային սերվեր (փուլ 3)

## Web Server (Nginx)


Nginx is another widely used webserver. 
Nginx is known for its high performance and low resource. 
Nginx can work as a standalone webserver or as front end reverse proxy of Apache web server, which will increase the performance.

If we have Apache server installed on the same Linux server, we need to ensure Apache and Nginx are running on different ports.
For example, we can change Apache config from default `80` port to `8080`. 

Edit below lines in `/etc/httpd/conf/httpd.conf`
```bash
#Listen 80
Listen 8080
```

Edit below lines in `/etc/httpd/conf.d/lt01.am.conf` 
```bash
#<VirtualHost *:80> 
<VirtualHost *:8080> 
```

Restart Apache:
```bash
systemctl restart httpd
```

Check that Apache listens port 8080: 
```bash
netstat -nlpt | grep httpd
```

Install Nginx:  
```bash
yum -y install nginx
```
Enable & start Nginx: 
```bash
systemctl enable --now nginx
```


#### PRACTICE
Now you should have running Apache webserver on port 8080, and Nginx on port 80

* Change Apache configuration to work on another port - 8888. After that you should be able to access `http://lt01.am:8888/`
* Change it back to 8080


### Nginx configuration
Create virtual host config file for our domain `lt01.am`
```bash
cat << EOF1 > /etc/nginx/conf.d/lt01.am.conf
server {
listen *:80;
access_log /var/log/nginx/lt01.am-access.log;
error_log /var/log/nginx/lt01.am-error.log;
server_name lt01.am www.lt01.am;
# Redirect to backend
location / {
proxy_pass http://127.0.0.1:8080/;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $remote_addr;
proxy_connect_timeout 120;
proxy_send_timeout 120;
proxy_read_timeout 180;
}
# Serve static pages with nginx
location ~* \.(jpg|jpeg|gif|png|ico|css|bmp|swf|js|html|txt)$ {
root /var/www/lt01.am;
}
}
EOF1
```
Ensure SELinux is tuned off (to allow redirection from Nginx to Apache): 
```bash
setenforce 0
```
Restart Nginx: 
```bash
systemctl restart nginx
```

Check that Nginx listens port 80: 
```bash
netstat -nlpt | grep nginx
```

Check the logs of both Nginx & Apache (open in different terminals to see simultaneously): 
```bash
tail -f /var/log/nginx/lt01.am-access.log
tail -f /var/log/httpd/lt01.am-access.log
```

Now try opening some URLs at Nginx and you should see in above both logs redirection to Apache server. 
```bash
links lt01.am
links lt01.am/inf.php
```

> REMINDER: the above will work only if: 
> * you have local DNS server, with properly configured zone `lt01.am` 
 and A record `lt01.am` pointing to your server's IP address
> * and your `/etc/resolv.conf` refers to `nameserver 127.0.0.1`

