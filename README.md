# wordpressbrain
Installed LEMP stack
PHP version: 8.1 with fpm
MySQL version: 8.0

WordPress Version Latest

Website: https://sarthak.serveblog.net
Document Root: /var/www/html/wordpress
Database: wordpress
user: wordpressuser


My Nginx Conf for VHOST
server {
            root /var/www/html/wordpress;
            index index.php index.html;
            server_name sarthak.serveblog.net;

            access_log /var/log/nginx/sarthak.serveblog.net.access.log;
            error_log /var/log/nginx/sarthak.serveblog.net.error.log;

            location / {
        try_files $uri $uri/ /index.php?$args;
       }

    # PHP Configuration
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Cache Configuration
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 1d;
        add_header Cache-Control "public, no-transform";
        proxy_cache my_cache;
        proxy_cache_valid 200 302 5m;
        proxy_cache_valid 404 1m;
    }


    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/sarthak.serveblog.net/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/sarthak.serveblog.net/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

}
server {
    if ($host = sarthak.serveblog.net) {
        return 301 https://$host$request_uri;
    }


            listen 80;
            server_name sarthak.serveblog.net;
            return 404;


}

My Nginx.conf

user www-data;
worker_processes auto;
worker_cpu_affinity auto;

error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
    multi_accept on;
    use epoll;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 30;
    types_hash_max_size 2048;
    server_tokens off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Log Format
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    # Gzip Compression
    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 5;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # SSL Settings (if using HTTPS)
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers off;
    ssl_session_timeout 5m;
    ssl_session_cache shared:SSL:10m;
    ssl_stapling on;
    ssl_stapling_verify on;

    # Caching Settings
    proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=100m inactive=60m;

    # Server Blocks (Virtual Hosts)
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}




