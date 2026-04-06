Chuẩn bài luôn! Việc sử dụng **`docker-compose.override.yml`** là "vũ khí" tối thượng để tách biệt môi trường. Bạn chỉ cần giữ file `docker-compose.yml` làm khung sườn (base), còn những thứ nhạy cảm hay khác biệt thì tống hết vào file override.

Để trang bị "tới tận răng" cho anh em dev và đảm bảo bạn (Admin) kê gối ngủ ngon, dưới đây là cấu trúc bộ công cụ hoàn chỉnh bạn nên đóng gói:

---

### 1. Cấu trúc File Chiến thuật
* **`docker-compose.yml`**: Chứa định nghĩa các service (PHP, MySQL, Redis) nhưng **không mở port**.
* **`docker-compose.override.yml`**: (Chỉ dành cho Dev - Local) Mở port 3306, 6379, 8000.
* **`docker-compose.prod.yml`**: (Chỉ dành cho Server) Chứa các cấu hình bảo mật, giới hạn RAM, không port.

### 2. File `bash docup` (Vũ khí cho Dev)
Thay vì bắt dev nhớ đống lệnh dài dòng, bạn tạo một file script `docup` (đặt trong thư mục gốc dự án) để tự động hóa mọi thứ:

```bash
#!/bin/bash

# 1. Kiểm tra file .env, nếu chưa có thì tạo từ .env.example
if [ ! -f .env ]; then
    cp .env.example .env
    echo "--- Đã tạo file .env mẫu ---"
fi

# 2. Khởi động Docker với các service cần thiết cho dev
echo "--- Đang khởi động môi trường Dev (WSL) ---"
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d

# 3. Chờ MySQL sẵn sàng một chút
echo "--- Đang đợi Database sẵn sàng... ---"
sleep 5

# 4. Tự động chạy Migration và Seed dữ liệu mẫu (Fake data)
docker compose exec app php artisan migrate --seed

echo "--- XONG! Anh em vào code tại: http://localhost:8000 ---"
```

---

### 3. Trang bị "Tận răng" trong `base.sh`
Để đảm bảo môi trường WSL của các dev y hệt nhau, file `base.sh` của bạn nên thực hiện thêm các bước "dọn dẹp" quyền ghi file ngay từ đầu:

```bash
# Trong file base.sh (Setup lần đầu cho Dev)
echo "Thiết lập quyền ghi file cho WSL..."
sudo chown -R $USER:www-data .
chmod -R 775 storage bootstrap/cache

# Cài đặt alias 'docup' vào bash_aliases của dev cho nó oai
if ! grep -q "alias docup" ~/.bash_aliases; then
    echo "alias docup='./docup'" >> ~/.bash_aliases
    source ~/.bash_aliases
fi
```



---

### 4. Quy trình cho Admin (Bạn) trên Server Pro
Khi bạn SSH qua **Cloudflare Zero Trust** vào server, bạn chỉ cần thực hiện quy trình "sạch" này:

1.  **Pull code mới nhất.**
2.  **Chạy lệnh deploy:**
    ```bash
    # Trên server, ta dùng file prod thay vì override
    docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
    ```
3.  **Xử lý quyền (nếu cần):** Docker trên server sẽ tự hiểu quyền dựa trên image bạn đã build.

---

### Một lưu ý nhỏ "về hưu" cho cái vụ DB:
Vì bạn đã chọn phương án **"WSL tự chơi với DB của mình"**, hãy dặn anh em dev:
> *"Nếu có thay đổi table, làm ơn tạo Migration. Tuyệt đối không được sửa cấu trúc DB bằng tay trong TablePlus/Navicat rồi quên push code!"*

Với bộ combo **`docker-compose.override.yml`** + **`docup`** + **`Cloudflare Zero Trust`**, hệ thống của bạn bây giờ đã:
* **Bảo mật:** DB Pro đóng kín cổng.
* **Tiện lợi:** Dev gõ đúng 1 lệnh `docup` là có đủ App + DB Fake data.
* **Native:** Code chạy trực tiếp trên WSL cực mượt.

Đến đây là bạn có thể yên tâm giao máy cho anh em "vào việc" rồi đấy. Bạn có định làm thêm một cái Dashboard nhỏ để theo dõi tình trạng các Container trên server không? Hay cứ dùng `docker ps` cho nó "Bash" truyền thống?

## Dùng set -e

```sh
#!/bin/bash
set -e

# Từ đây, bất kỳ lệnh nào trả về Exit Code khác 0 (thất bại), 
# script sẽ dừng lại ngay lập tức.
```

-e (errexit): Dừng khi có lệnh lỗi.

-u (nounset): Dừng nếu bạn dùng một biến chưa được định nghĩa (tránh lỗi đánh máy tên biến).

-o pipefail: Nếu bạn dùng ống dẫn (pipe) như A | B | C, bình thường nếu A lỗi nhưng C thành công thì Bash vẫn coi là thành công. pipefail sẽ bắt lỗi ngay cả khi nó nằm ở đầu chuỗi pipe.

>> docker run -it --name lab-ubuntu -v $(pwd):/work -w /work ubuntu:22.04 bash

docker run -it --name lab-ubuntu \
  --memory="512m" --cpus="0.5" \
  -v $(pwd):/work -w /work ubuntu:22.04 bash

  docker run -it --name lab-ubuntu \
  --memory="512m" \
  --memory-swap="2g" \
  --cpus="0.5" \
  -v $(pwd):/work -w /work ubuntu:22.04 bash


  # Giới hạn container chỉ được chiếm tối đa 10GB ổ cứng
docker run -it --name lab-ubuntu \
  --storage-opt size=10G \
  ... (các tham số khác)