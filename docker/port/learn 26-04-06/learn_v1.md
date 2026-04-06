# Cấu hình Docker Compose (Mẫu)
```yml
version: '3.8'

services:
  # Container chạy Cloudflare Tunnel
  cloudflared:
    image: cloudflare/cloudflared:latest
    command: tunnel run --token <YOUR_TUNNEL_TOKEN>
    networks:
      - dev_network

  # Container Ubuntu cho người dev 1
  ubuntu_dev1:
    image: ubuntu:latest
    container_name: dev_user_1
    networks:
      - dev_network
    # (Thêm các cấu hình cài đặt môi trường dev ở đây)

  # Container Ubuntu cho người dev 2
  ubuntu_dev2:
    image: ubuntu:latest
    container_name: dev_user_2
    networks:
      - dev_network

networks:
  dev_network:
    driver: bridge
```

## Lưu ý cực kỳ quan trọng cho người Dev

php artisan serve --host=0.0.0.0 --port=8000

## Chạy nhiều file Compose độc lập

docker-compose -f docker-compose.prod.yml up -d
## Tại thư mục Dev (hoặc cùng thư mục nhưng file khác)
docker-compose -f docker-compose.dev1.yml up -d
docker-compose -f docker-compose.dev2.yml up -d

### Lưu ý quan trọng về "Project Name":

Mặc định, Docker Compose lấy tên thư mục làm tên Project. Nếu bạn chạy nhiều file trong cùng 1 thư mục, chúng sẽ bị lẫn lộn. Để tách hẳn ra, bạn dùng cờ -p (Project Name):

docker-compose -p prod -f prod.yml up -d

docker-compose -p dev_user1 -f dev1.yml up -d

## Sử dụng cơ chế "Override" (Kế thừa)

Đây là cách phổ biến khi bạn có một bộ khung giống nhau nhưng cấu hình cho Dev và Prod khác nhau.

docker-compose.yml: Chứa khung chung (Nginx, PHP...).

docker-compose.override.yml: (Mặc định Docker sẽ tự đọc file này) Chứa các tùy chỉnh cho Dev (ví dụ: mount code trực tiếp từ máy host vào để dev cho nhanh).

docker-compose.prod.yml: Chứa các tùy chỉnh bảo mật cho Prod (ví dụ: tắt port database, đặt password mạnh, backup tự động).

> docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d

# Bản chất kỹ thuật (Docker Engine)

```yml
version: '3.8'

services:
  # KHU VỰC PRODUCTION
  db_prod:
    image: mysql:8.0
    container_name: database_xin_so
    networks:
      - prod_network  # Chỉ nằm trong mạng Prod
    # KHÔNG mở ports ra ngoài máy host

  app_prod:
    image: laravel_prod_image
    networks:
      - prod_network  # Nằm trong mạng Prod để gọi được db_prod
      - public_network # Nằm thêm mạng này để Cloudflare Tunnel thấy được nó

  # KHU VỰC DEV (Ông dev "múa" ở đây)
  ubuntu_dev_user1:
    image: ubuntu:latest
    networks:
      - dev_network   # Chỉ nằm trong mạng Dev
    # Ông này sẽ KHÔNG THỂ ping thấy "database_xin_so"

# ĐỊNH NGHĨA CÁC MẠNG RIÊNG BIỆT
networks:
  prod_network:
    driver: bridge # Mạng nội bộ cho Prod
  dev_network:
    driver: bridge # Mạng nội bộ cho Dev
  public_network:
    driver: bridge # Mạng để giao tiếp với Cloudflare Tunnel
```

## Quy tắc "Biết Port" trong mô hình Prod/Dev của bạn

Port Nội Bộ	expose: - "3306"

Port Công Cộng	ports: - "8080:80"

### Mẹo kiểm tra kết nối giữa các mạng
# Đứng từ container của ông Dev (mạng dev_network)
docker exec -it ubuntu_dev_container ping db_prod

# Đứng từ container Laravel Prod (mạng prod_network)
docker exec -it laravel_prod_container ping db_prod

## Chỉ định Port cụ thể trên Localhost (An toàn hơn)

ports:
  - "127.0.0.1:3306:3306"

## Cách "Chỉ định" Port cho các ông Dev (Để không bị loạn)

```yml
services:
  ubuntu_dev1:
    container_name: user_cong_dev
    networks:
      - dev_network
    # Ép ông dev luôn dùng port 8000 nội bộ
    environment:
      - APP_PORT=8000
```

# Dùng docker inspect: Sau khi deploy Prod, hãy tập thói quen chạy:
docker inspect <container_name> | grep HostPort

Production: Không dùng ports. Chỉ dùng networks.

Access nội bộ máy: Dùng 127.0.0.1:port:port.