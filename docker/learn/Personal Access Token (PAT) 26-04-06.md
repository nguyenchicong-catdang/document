# Personal Access Token (PAT)

Cách tiếp cận của bạn rất chuẩn: thay vì nhập mật khẩu thủ công mỗi lần (giống như cách chúng ta dùng SSH Key cho Git), Docker sử dụng một cơ chế an toàn hơn gọi là **Personal Access Token (PAT)**.

Việc dùng Token thay vì mật khẩu chính là "chìa khóa" để bạn tự động hóa trên server mà không sợ lộ mật khẩu chính của tài khoản Docker Hub.

---

### 1. Tạo "Chìa khóa" (Personal Access Token)
Bạn không thể add SSH Key trực tiếp vào Docker Hub như GitHub, nhưng bạn có thể tạo Token để dùng thay mật khẩu:

1.  Đăng nhập vào [Docker Hub](https://hub.docker.com/).
2.  Click vào **Avatar** (góc trên bên phải) -> **Account Settings**.
3.  Chọn mục **Security** -> **Personal Access Tokens**.
4.  Nhấn **Generate new token**.
    * **Description:** Đặt tên là `Server-ZeroTrust` (để biết key này dùng cho máy nào).
    * **Access permissions:** Chọn `Read, Write, Delete` (để có quyền push/pull).
5.  **Copy Token này và lưu lại** (nó chỉ hiện một lần duy nhất).

---

### 2. "Add Key" vào Server để không phải nhập lại
Thay vì gõ lệnh rồi đợi nó hỏi mật khẩu, bạn có thể "pipe" (truyền) token vào thẳng lệnh login. Điều này giúp bạn có thể viết vào file `.bash_aliases` để dùng cho tiện.

**Cách 1: Lưu Token vào một file ẩn (An toàn nhất)**
1. Lưu token vào file: `echo "YOUR_TOKEN_HERE" > ~/.docker_token`
2. Cấp quyền chỉ mình bạn được đọc: `chmod 600 ~/.docker_token`
3. Đăng nhập bằng lệnh:
```bash
cat ~/.docker_token | docker login --username YOUR_DOCKER_ID --password-stdin
```

**Cách 2: Tạo Alias trong Bash (Tiện nhất cho workflow của bạn)**
Vì bạn thường xuyên dùng `.bash_aliases` trên Ubuntu/WSL, hãy thêm dòng này vào file đó:

```bash
# Mở file alias
nano ~/.bash_aliases

# Thêm dòng này vào cuối (thay đổi thông tin của bạn)
alias dlogin='echo "YOUR_TOKEN_HERE" | docker login --username congdev --password-stdin'
```
Sau đó chạy `source ~/.bash_aliases`. Từ giờ, mỗi lần cần đăng nhập lại trên server, bạn chỉ cần gõ đúng một từ: `dlogin`.

---

### 3. Tại sao cách này lại giống "Git Key"?
* **Không lưu mật khẩu gốc:** Nếu server bị lộ, bạn chỉ cần lên Docker Hub xóa Token đó đi là xong, mật khẩu chính vẫn an toàn.
* **Ghi nhớ vĩnh viễn:** Sau khi `docker login` thành công một lần, Docker sẽ lưu thông tin xác thực đã mã hóa vào file `~/.docker/config.json`. Bạn sẽ không phải login lại cho đến khi token hết hạn hoặc bạn chủ động `docker logout`.

### 4. Mẹo nâng cao cho Docker trên Server
Nếu bạn đang build các image nặng (như Laravel + Node.js), hãy tận dụng tính năng **Buildx** để tăng tốc và tận dụng cache tốt hơn, tương tự như cách bạn tối ưu Dockerfile Alpine:

```bash
docker buildx build --push -t username/repo:tag .
```

Cách này sẽ vừa build vừa push ngay lập tức sau khi xong, rất phù hợp khi làm việc qua SSH/Cloudflare Tunnel vì nó tối ưu hóa luồng dữ liệu gửi đi.

Bạn đã tạo được Token trên Docker Hub chưa, hay cần tôi hướng dẫn kỹ hơn phần nào trong giao diện của nó không?

> docker buildx build --push -t username/repo:tag .

# Gợi ý cho file base.sh của bạn

```bash
# Trong base.sh, hãy thêm phần kiểm tra quyền file
echo "Cấu hình quyền cho Laravel..."
sudo chown -R $USER:www-data storage bootstrap/cache
chmod -R 775 storage bootstrap/cache

# Cài đặt một công cụ quản lý DB nhẹ nhàng cho anh em dev
sudo apt install -y sqlite3 # Nếu bạn muốn dev nhanh không cần bật Docker DB
```

# Gán quyền sở hữu cho user chạy web server bên trong container
chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache
# Cấp quyền ghi
chmod -R 775 /var/www/html/storage /var/www/html/bootstrap/cache

[automount]
options = "metadata,umask=22,fmask=11"

Cách 1: Dùng "Cầu nối" SSH Tunnel (Khuyên dùng cho Admin/Dev)
Vì bạn đã thiết lập được VS Code SSH qua Cloudflare Zero Trust (như ở câu hỏi đầu tiên), bạn có một "đường ống" cực kỳ bảo mật.

Cấu hình SSH: Khi bạn SSH vào server, bạn có thể "mượn" cổng 3306 của Server về máy WSL của mình.

Lệnh thực hiện (trên WSL của Dev):

Bash
# Lệnh này bảo: "Lấy cổng 3306 của Server, gắn nó vào cổng 3307 của máy tôi"
ssh -L 3307:127.0.0.1:3306 user@server-biet-danh