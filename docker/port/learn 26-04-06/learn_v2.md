# Thiết lập "Vùng cấm" (Reserved Port) cho Production.

```yml
# Thư mục /produc/docker-compose.yml
services:
  app_pro:
    image: your-pro-image
    ports:
      - "8080:80" # Cố định: Port 8080 của máy nhà -> Port 80 container
    restart: always
```
## Cách B: Ép dải Port cho Dev

```yml
# Thư mục /dev/docker-compose.yml
services:
  ubuntu_dev:
    ports:
      - "9001:8000" # Ông dev chỉ được múa ở vùng 9000+
```

## kiểm tra

sudo netstat -tulpn | grep :8080
# Hoặc
sudo lsof -i :8080

## Giải pháp: Bạn phải giới hạn tài nguyên trong Compose:

```yml
deploy:
  resources:
    limits:
      cpus: '0.50'
      memory: 512M
```

# Cách kiểm tra máy bạn có bao nhiêu "đơn vị CPU"

> nproc

deploy:
  resources:
    limits:
      cpus: '0.50'    # Tối đa không được vượt quá (Cái trần)
      memory: 512M
    reservations:
      cpus: '0.10'    # Luôn luôn giữ chỗ sẵn (Đảm bảo tối thiểu)
      memory: 128M

# Dưới đây là cách hiện thực hóa chiến thuật "Dùng ổ cứng cứu RAM"

```yml
mysql:
    image: mysql:8.0
    deploy:
      resources:
        limits:
          memory: 512M        # RAM thật (limit)
          # memory-swap: 1G   # Tổng (RAM 512 + Swap 512) - Lưu ý: WSL cần bật swap trước
```

## Chặn SCP và SFTP (Chỉ cho phép gõ lệnh)

# Tắt SFTP
# Subsystem sftp /usr/lib/openssh/sftp-server (Comment dòng này lại)