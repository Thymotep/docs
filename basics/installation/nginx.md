---
title: Configure Nginx
menuTitle: Nginx
weight: 25
---

# Configure Nginx

The following file is provided as an example configuration for your Nginx server.
It may be incomplete, and remember you must adapt it for your own server's needs!

```nginx
server {
    # Ipv4
    listen 80;

    # IPv6
    # listen [::]:80;

    # SSL Ipv4 & v6
    # listen 443 ssl http2;
    # listen [::]:443 ssl;

    # Your SSL Certificates, don't forget to take a look at Certbot (https://certbot.eff.org)
    # ssl_certificate /etc/ssl/fullchain.pem;
    # ssl_certificate_key /etc/ssl/privkey.pem;

    # ssl_session_timeout 24h;
    # ssl_session_cache shared:SSL:10m;
    # ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    # ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:RSA+3DES:AES128-SHA:!ADH:!AECDH:!MD5;
    # ssl_prefer_server_ciphers on;
    # Do not forget to create this file before with OpenSSL : "openssl dhparam -out /etc/nginx/ssl/dhparam.pem 2048"
    # ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    # [REQUIRED EDIT] Your domain name goes here
    server_name server_name example.com www.example.com;

    # [REQUIRED EDIT] Absolute path to your website root on the filesystem
    root /path/to/prestashop;

    index index.php;

    # Redirect 404 errors to prestashop
    error_page 404 /index.php?controller=404;

    # Gzip Settings, convert all types.
    gzip on;
    gzip_vary on;
    gzip_proxied any;

    # Can be enhance to 5, but it can slow you server
    # gzip_comp_level    5;
    # gzip_min_length    256;

    gzip_types
        application/atom+xml
        application/javascript
        application/json
        application/ld+json
        application/manifest+json
        application/rss+xml
        application/vnd.geo+json
        application/vnd.ms-fontobject
        application/x-font-ttf
        application/x-web-app-manifest+json
        application/xhtml+xml
        application/xml
        font/opentype
        image/bmp
        image/svg+xml
        image/x-icon
        text/cache-manifest
        text/css
        text/plain
        text/vcard
        text/vnd.rim.location.xloc
        text/vtt
        text/x-component
        text/x-cross-domain-policy;

    gzip_disable "MSIE [1-6]\.(?!.*SV1)";

    # HSTS (Force clients to interact with your website using HTTPS only)
    # For enhanced security, register your site here: https://hstspreload.org/
    # WARNING: Don't use this if your site is not fully on HTTPS!
    # add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" preload; always;

    # Cloudflare / Max CDN fix
    location ~* \.(eot|otf|ttf|woff(?:2)?)$ {
        add_header Access-Control-Allow-Origin *;
    }

    # Force pdf files to be downloaded
    location ~* \.pdf$ {
        add_header Content-Disposition Attachment;
        add_header X-Content-Type-Options nosniff;
    }

    # Force files in upload directory to be downloaded
    location ~ ^/upload/ {
        add_header Content-Disposition Attachment;
        add_header X-Content-Type-Options nosniff;
    }

    # Do not save logs for these
    location = /favicon.ico {
        auth_basic off;
        allow all;
        log_not_found off;
        access_log off;
    }

    location = /robots.txt {
        auth_basic off;
        allow all;
        log_not_found off;
        access_log off;
    }

    # [REQUIRED EDIT] if you are using multiple languages
    # rewrite ^/fr$ /fr/ redirect;
    # rewrite ^/fr/(.*) /$1;

    # Images
    rewrite ^/([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$1$2$3.jpg last;
    rewrite ^/([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$1$2$3$4.jpg last;
    rewrite ^/([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$1$2$3$4$5.jpg last;
    rewrite ^/([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$1$2$3$4$5$6.jpg last;
    rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$1$2$3$4$5$6$7.jpg last;
    rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$1$2$3$4$5$6$7$8.jpg last;
    rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$7/$1$2$3$4$5$6$7$8$9.jpg last;
    rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$7/$8/$1$2$3$4$5$6$7$8$9$10.jpg last;
    rewrite ^/c/([0-9]+)(-[.*_a-zA-Z0-9-]*)(-[0-9]+)?/.+.jpg$ /img/c/$1$2$3.jpg last;
    rewrite ^/c/([a-zA-Z_-]+)(-[0-9]+)?/.+.jpg$ /img/c/$1$2.jpg last;

    # AlphaImageLoader for IE and fancybox
    rewrite ^images_ie/?([^/]+)\.(jpe?g|png|gif)$ js/jquery/plugins/fancybox/images/$1.$2 last;

    # Web service API
    rewrite ^/api/?(.*)$ /webservice/dispatcher.php?url=$1 last;

    # Installation sandbox
    rewrite ^(/install(?:-dev)?/sandbox)/(.*) /$1/test.php last;
    
    # without this line nginx seo url not working.
    try_files $uri $uri/ /index.php?$args;
     
    # [REQUIRED EDIT] Change this block to your admin folder
    location /admin-dev/ {
        if (!-e $request_filename) {
            rewrite ^/.*$ /admin-dev/index.php last;
        }
    }


    # File security
    # .htaccess .DS_Store .htpasswd etc
    location ~ /\. {
        deny all;
    }
    # Source code directories
    location ~ ^/(app|bin|cache|classes|config|controllers|docs|localization|override|src|tests|tests-legacy|tools|translations|travis-scripts|vendor|var)/ {
        deny all;
    }
    # vendor in modules directory
    location ~ ^/modules/.*/vendor/ {
        deny all;
    }
    # Prevent exposing other sensitive files
    location ~ \.(yml|log|tpl|twig|sass)$ {
        deny all;
    }

    # Prevent injection of php files
    location /upload {
        location ~ \.php$ {
            deny all;
        }
    }
    location /img {
        location ~ \.php$ {
            deny all;
        }
    }

    # PHP FPM part
    location ~ \.php$ {
        # Verify that the file exists, redirect to index if not
        try_files $fastcgi_script_name /index.php$uri&$args =404;

        fastcgi_index  index.php;

        # Environment variables for PHP
        fastcgi_split_path_info ^(.+\.php)(/.+)$;

        include       fastcgi_params;

        fastcgi_param PATH_INFO       $fastcgi_path_info;
        fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        # [REQUIRED EDIT] Connection to PHP-FPM - choose one
        # fastcgi_pass 127.0.0.1:9000;
        fastcgi_pass unix:/run/php/php7.3-fpm.sock;

        fastcgi_keep_conn on;
        fastcgi_read_timeout 30s;
        fastcgi_send_timeout 30s;

        # In case of long loading or 502 / 504 errors
        # fastcgi_buffer_size 256k;
        # fastcgi_buffers 256 16k;
        # fastcgi_busy_buffers_size 256k;
        client_max_body_size 10M;
    }
}
```
