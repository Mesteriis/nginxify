# nginxify

Make a site-enable config for NGINX via an API

----------


What the heck is this thing?
----------------------------

I wanted to be able to automatically add a reverse proxy entry to NGINX.  Ideal if you have subdomains per customer, and you're deploying new web assets via Docker containers or with boring servers.


**Flow**

![Diagram](https://s3.amazonaws.com/timski-pictures/nginxify.jpg)


**Default NGINX Template**

    server {
      listen       80;
      server_name  {{ server_name }};
    
      location ~ /(.git|.svn|README.md) {
        deny all;
        log_not_found off;
        access_log off;
        return 404;
      }
    
      location = /favicon.ico {
        log_not_found off;
        access_log off;
      }
    
      location / {
        try_files $uri @service;
      }
    
      location @service {
        proxy_pass http://{{ proxy_addr }}:{{ port }};
        proxy_redirect off;
        proxy_buffering off;
    
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
      }
    }

**To Do's**
 
  - Delete configurations
  - Hard limit new configurations added (round robin like feature)
