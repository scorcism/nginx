# NGINX

#### Install 

```bash
sudo apt update

sudo apt install nginx

sudo systemctl status nginx

```

when you checkout your ip adress you will see a welcome page 

so, from where that welcome page comes from ?

and where its is configured

it is present in ```/etc/nginx/``` -> all these are created when we installed nginx.

The imprtant file is `nginx.conf` which is the entry point of nginx. 

what inside that file ?

```conf

user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
#
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}


```

This `include /etc/nginx/modules-enabled/*.conf;` is called as the directive in nginx, whichever file we include here that file will be included here. 

This says inside the directory include all the files with .conf extension.

## Configuration

nginx configuration is in special stucture. Like a Tree sturcture.

1st block(contest) main contest [we list all the things whcih will happen here ] 

nginx runs which has a master process block and some worker process to handle connections. 

which includes

1) number of worker process

2) username

3) PID

4)  log location

## Inside main

1st block inside main

events block

as we are seeing main block as a content of number of worker process so events block maintas the numbr of worker process and each worker process will handle how much connections 

aka number of conetions per working process

2nd 

stream -> handle  level 3 || 4 settings

3rd 

http block -> imp 

here we define how to process requests

Inside http block

1st 

server

create virtual servers or hosts

2nd
upstream

used to work the nginx as a reverse proxy. used as a locad balacer

Inside server blobk

location block

routing is done here. Matching routing 

![blocks](https://imgur.com/pb3fzlg.png)

http can have multiple server blocks and each sever block can have multiple location block

```conf

# Main context
user www-data; # user who started nginx process name
worker_processes auto; # count ofworker pricesses
pid /run/nginx.pid; # PID
include /etc/nginx/modules-enabled/*.conf; # all other configurate

# block 1, inside main
events {
	worker_connections 768; # connection count per worker process
	# multi_accept on;
}

# block 2, inside main
# imp
http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log; # log file
	error_log /var/log/nginx/error.log;

	#########
	# access_log , error_log, location are called directives
	#########

	##
	# Gzip Settings
	##

	gzip on; # for compression

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf; ## all the files will be enbedded here
	include /etc/nginx/sites-enabled/*; ##  
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
#
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}


```

Inside conf.d -> as per usage create conf file inside this

folder sites-enabled has a file default

```conf

##
# You should look at the following URL's in order to grasp a solid understanding
# of Nginx configuration files in order to fully unleash the power of Nginx.
# https://www.nginx.com/resources/wiki/start/
# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
# https://wiki.debian.org/Nginx/DirectoryStructure
#
# In most cases, administrators will remove this file from sites-enabled/ and
# leave it as reference inside of sites-available where it will continue to be
# updated by the nginx packaging team.
#
# This file will automatically load configuration files provided by other
# applications, such as Drupal or Wordpress. These applications will be made
# available underneath a path with that package name, such as /drupal8.
#
# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
##

# Default server configuration
#
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	# SSL configuration
	#
	# listen 443 ssl default_server;
	# listen [::]:443 ssl default_server;
	#
	# Note: You should disable gzip for SSL traffic.
	# See: https://bugs.debian.org/773332
	#
	# Read up on ssl_ciphers to ensure a secure configuration.
	# See: https://bugs.debian.org/765782
	#
	# Self signed certs generated by the ssl-cert package
	# Don't use them in a production server!
	#
	# include snippets/snakeoil.conf;

	root /var/www/html; ## all files locations

	# Add index.php to the list if you are using PHP
	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

	# pass PHP scripts to FastCGI server
	#
	#location ~ \.php$ {
	#	include snippets/fastcgi-php.conf;
	#
	#	# With php-fpm (or other unix sockets):
	#	fastcgi_pass unix:/run/php/php7.4-fpm.sock;
	#	# With php-cgi (or other tcp sockets):
	#	fastcgi_pass 127.0.0.1:9000;
	#}

	# deny access to .htaccess files, if Apache's document root
	# concurs with nginx's one
	#
	#location ~ /\.ht {
	#	deny all;
	#}
}


# Virtual Host configuration for example.com
#
# You can move that to a different file under sites-available/ and symlink that
# to sites-enabled/ to enable it.
#
#server {
#	listen 80;
#	listen [::]:80;
#
#	server_name example.com;
#
#	root /var/www/example.com;
#	index index.html;
#
#	location / {
#		try_files $uri $uri/ =404;
#	}
#}


```

Its recommended to use different config files for diff clients so 
comments the site enabled
and create  file inside directory /etc/nginx/conf.d

```bash

sudo vim abhishek.com/conf

server {
	
	listen 80 default_server;
	root /var/www/abhishek; # all content folder
		
	
	server_name _; # domain name catch all

	index index.html index.htm; # which file to access  inside root

	location / { # / means match all the pth eg /api will match api points
		
	# try_files $uri # match uri 	
	
	
	try_files $uri $uri/ =404; # if not path then show 404 page


	}
}

 ```

adding basic authentication

```conf

sudo vim abhishek.com/conf

server {
	
	listen 80 default_server;
	root /var/www/abhishek; 
		
	
	server_name _;

	####
	# auth

	auth_basic "under development site";
	auth_basic_user_file /etc/nginx/.htpasswd; # this file will contain username and pasword using special tool

	###

	index index.html index.htm; 

	location / { 
	
	try_files $uri $uri/ =404; 

	}
}


```

generate .htpassword 

ther are multiple tools we can use openssl htpasswd and many others


we will use open ssh 

```bash
sh -c "echo -n 'abhishek:' >> /etc/nginx/.htpasswd"

sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"

```

allow some route and block some route for paaword
like eg you want to show /home and block /admin


```conf

sudo vim abhishek.com/conf

server {
	
	listen 80 default_server;
	root /var/www/abhishek; 
		
	
	server_name _;

	####
	# auth

	auth_basic "under development site";
	auth_basic_user_file /etc/nginx/.htpasswd; # this file will contain username and pasword using special tool

	###

	index index.html index.htm; 

	location /home { 
		auth_basic off;
		try_files $uri $uri/ =404; 
	}

	location /admin { 
		
		try_files $uri $uri/ =404; 

	}
}


```


server controls

```bash

sudo nginx -t # test nginx config file 

sudo systemctl reload nginx # reload server

sudo nginx -T # show all configurations 

```



nginx as a reverse proxy 

```conf

upstrem anyname {
	server localhost:8000;
}

server{
	listen 80

	server_name api.x.com

	location / {
		proxy_pass http://anyname;
	}
}

```


load balancing


```conf

upstrem anyname {
	server localhost:8000;
	server localhost:8001;
}

server{
	listen 80

	server_name api.x.com

	location / {
		add_header Cache-Control no-store; # avoid caching
		proxy_pass http://anyname;
	}
}

```

change the role ups


```conf

upstrem anyname {
	server localhost:8000 weight=3; # this will handle 3 request then next 1 will be handled by 8001 then again 3 by 8000
	server localhost:8001;
	server localhost:8002 backup; # use this as a backup server
	server localhost:8003 down; # Mark the server down 
}

server{
	listen 80

	server_name api.x.com

	location / {
		add_header Cache-Control no-store; # avoid caching
		proxy_pass http://anyname;
	}
}

```

host react app

```conf

server {

        listen 80 default_server;
        root /var/www/build; # all content folder


        server_name _;

        index index.html index.htm; # which file to access  inside root

        location / {
        try_files $uri $uri/ /index.html # if we dont have a page show /index page 

        }
}


```

## add tls

using certbot 
choose your requirement from the server

install snapd
follow guice

### How do we prevent user from calling direct ip access and get website? 


Well, In order to block ip access, you would add a new server block ( maybe in new .conf file )
There you have do following,

```
server {
    listen      80 default_server;
    listen      [::]:80 default_server;
    server_name "";
    return      444;
}
