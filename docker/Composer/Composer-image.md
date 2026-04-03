```yml
services:
  # 1. App chính của bạn (PHP-Alpine siêu nhẹ)
  laravel-app:
    build: 
      context: .
      dockerfile: Dockerfile-alpine
    volumes:
      - .:/var/www/html # Mount code từ máy thật vào container

  # 2. "Thợ chạy thuê" Composer
  composer:
    image: composer:latest
    container_name: composer
    volumes:
      - .:/app # Mount chung thư mục gốc của Laravel
    working_dir: /app
    # Lệnh này giúp composer chạy xong là tự nghỉ, không tốn tài nguyên
    entrypoint: ["composer", "--ignore-platform-reqs"]
```

```docker
# Giai đoạn 1: Builder (Có Composer)
FROM composer:latest AS builder
WORKDIR /app
COPY . .
RUN composer install --no-dev --optimize-autoloader

# Giai đoạn 2: Production (PHP-Alpine siêu sạch)
FROM php:8.2-alpine
WORKDIR /var/www/html
# CHỈ COPY thư mục vendor từ builder sang
COPY --from=builder /app/vendor ./vendor
COPY . .

# Image cuối cùng của bạn sẽ cực nhẹ vì không chứa Composer hay cache của nó.
```