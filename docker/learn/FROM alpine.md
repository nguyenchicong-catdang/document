## FROM alpine

```dockerfile
FROM alpine:3.18

# 1. Cài đặt Nginx và PHP-FPM từ "chợ" apk của Alpine
# Ở đây bạn phải cài thêm các extension PHP vì bản này nó "trần trụi" lắm
RUN apk add --no-cache \
    nginx \
    supervisor \
    php82-fpm \
    php82-pdo_sqlite \
    php82-json \
    php82-openssl \
    php82-curl

# 2. Alpine cài php vào thư mục có số 82, nên ta tạo link cho dễ gọi
RUN ln -s /usr/bin/php82 /usr/bin/php

WORKDIR /var/www/html
COPY . .
# ... copy config ...
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]
```


## note RUN docker-php-ext-install pdo pdo_sqlite
FROM php:8.2-fpm-alpine

## services:
  app:
    image: my-app
    deploy:
      resources:
        limits:
          memory: 256M  # Không cho phép ăn quá 256MB

## logging:
  driver: "json-file"
  options:
    max-size: "10m"
    max-file: "3"

Backend: Octane chạy trên nền Swoole hoặc RoadRunner (đều là các server hiệu năng cao viết bằng C hoặc Go).

## Bước 1: Cập nhật Dockerfile

FROM php:8.2-fpm-alpine

# 1. Cài thêm phần phụ trợ
RUN apk add --no-cache nginx supervisor libcap

# 2. Cài Extension cần thiết cho Octane
RUN docker-php-ext-install pcntl pdo pdo_sqlite

# 3. Cài RoadRunner thông qua Composer (hoặc tải binary)
WORKDIR /var/www/html
COPY . .
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer install
RUN php artisan octane:install --server=roadrunner

# 4. Thay vì chạy PHP-FPM, Supervisor sẽ chạy Octane
COPY ./docker/supervisord.conf /etc/supervisord.conf

# Octane mặc định chạy cổng 8000
EXPOSE 8000

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]

Bước 2: Cấu hình Supervisord cho Octane

```conf
[program:laravel-octane]
command=php artisan octane:start --server=roadrunner --host=0.0.0.0 --port=8000
autostart=true
autorestart=true
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stderr
stderr_logfile_maxbytes=0
```

Giải pháp: Octane có cơ chế --max-requests=1000 (tự khởi động lại sau 1000 request để xả RAM).

## Kết nối Nginx (Host) -> Docker (Octane)

```nginx
server {
    listen 80;
    server_name khach-hang-a.com;

    location / {
        proxy_pass http://127.0.0.1:8000; # Port của Container Octane
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```nginx
location ~ \.php$ {
    # 127.0.0.1 ở đây là CHÍNH NÓ (bên trong container)
    fastcgi_pass 127.0.0.1:9000; 
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
```

```nginx
location / {
    # Nginx gọi sang Octane (cũng nằm trong container đó luôn)
    proxy_pass http://127.0.0.1:8000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
}
```

> try_files $uri $uri/ /index.php?$query_string;

> Octane biến PHP thành một thực thể chạy liên tục trong RAM. Tùy vào "nhân" bạn chọn (Swoole hay RoadRunner) mà nó có cơ chế khác nhau:

```sql
PRAGMA journal_mode=WAL;
PRAGMA synchronous=NORMAL;
```

```conf
[supervisord]
nodaemon=true
user=root

[program:nginx]
command=nginx -g "daemon off;"
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stderr
stderr_logfile_maxbytes=0

[program:octane]
# Chạy Octane với RoadRunner
command=php artisan octane:start --server=roadrunner --host=0.0.0.0 --port=8000 --max-requests=1000
user=www-data
autostart=true
autorestart=true
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stderr
stderr_logfile_maxbytes=0
```