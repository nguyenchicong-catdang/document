# Tạo thư mục quản lý tập trung
```bash
mkdir -p ~/scripts
```
# Nối lệnh trong file Script (Tổ chức theo hàng dọc)
```bash
nano ~/scripts/setup-ws.sh
```
## Mẫu file ~/scripts/setup-ws.sh để tổ chức lệnh:
```bash
#!/bin/bash
echo "--- Đang khởi động dự án ---"
cd ~/ws && ls
# Bạn có thể thêm nhiều lệnh khác ở đây, mỗi lệnh một dòng
```
## Cấp quyền thực thi (Chỉ cần làm 1 lần duy nhất)
```bash
chmod +x ~/scripts/setup-ws.sh
```
### Chạy thử file script
```bash
# Chạy trực tiếp bằng đường dẫn (Phổ biến nhất)
~/scripts/setup-ws.sh
# Chạy thông qua trình thông dịch Bash
bash ~/scripts/setup-ws.sh
# Chạy và giữ lại môi trường (Dùng lệnh source hoặc .)
source ~/scripts/setup-ws.sh
```
## run terminal
```bash
wsl.exe -d Ubuntu-24.04 -e bash -c "~/scripts/setup-ws.sh; exec bash"
```
wsl.exe -d Ubuntu-24.04 -e bash ~/scripts/setup-ws.sh
## tham khảo
```bash
#!/usr/bin/expect -f

# Tắt việc hiển thị các dòng lệnh spawn hệ thống
log_user 0

# 1. Chạy ngầm việc nạp Agent và SSH Key
spawn bash -c "eval \$(ssh-agent -s) && ssh-add /home/cong/.ssh/id_ed25519"
expect "Identity added"
expect eof

# Bật lại hiển thị để thấy kết quả các lệnh bên dưới
log_user 1

# 2. Khởi động giao diện làm việc
spawn bash -c "
    echo '====================================';
    echo '   HỆ THỐNG ĐÃ SẴN SÀNG             ';
    echo '====================================';
    cd ~/document;
    ls -F;
    exec bash
"

interact
```

## v2
```bash
#!/bin/bash

echo "--- Đang khởi động dự án ---"

# Nối nhiều lệnh nhưng vẫn tách dòng để dễ nhìn
cd ~/document && \
ls -la && \
echo "Đã vào thư mục Document"

exec bash
```
### v3
```bash
#!/bin/bash

# Lệnh 1: Hiển thị thông báo
echo "--- Đang khởi động dự án ---"

# Lệnh 2: Thêm Key SSH (Vì bạn đã bỏ pass nên lệnh này chạy ngầm rất nhanh)
ssh-add /home/cong/.ssh/id_ed25519

# Lệnh 3: Di chuyển thư mục
cd ~/document

# Lệnh 4: Liệt kê file
ls

# Lệnh 5: Giữ Terminal mở
exec bash
```