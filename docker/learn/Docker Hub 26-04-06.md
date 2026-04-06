Để push các image bạn đã build từ server Linux lên **Docker Hub**, quy trình thực hiện qua terminal (Bash) rất thẳng hàng và logic. Vì bạn đang dùng Docker Engine trên server, bạn sẽ thao tác hoàn toàn qua lệnh `docker`.

Dưới đây là 3 bước để đưa "đứa con tinh thần" của bạn lên Cloud:

---

### 1. Đăng nhập vào Docker Hub từ Terminal
Trước khi push, server cần biết bạn là ai. Hãy chạy lệnh sau:

```bash
docker login
```
* **Username:** Nhập tên tài khoản Docker Hub của bạn (không phải email).
* **Password:** Nhập mật khẩu. 
    * *Lưu ý bảo mật:* Nếu bạn bật 2FA (xác thực 2 lớp), hãy vào Docker Hub tạo một **Access Token** và dùng token đó thay cho mật khẩu.

### 2. Gắn thẻ (Tag) cho Image đúng cấu trúc
Đây là bước quan trọng nhất. Docker Hub chỉ chấp nhận các image có tên theo định dạng: `username/ten-image:tag`.

Giả sử bạn có một image tên là `my-laravel-app` vừa build xong, và username Docker Hub của bạn là `congdev`:

```bash
# Cú pháp: docker tag <tên-gốc> <username>/<tên-mới>:<phiên-bản>
docker tag my-laravel-app congdev/my-laravel-app:v1.0
```

*Nếu bạn không để `:v1.0`, Docker sẽ mặc định gán tag là `:latest`.*

### 3. Push Image lên Docker Hub
Sau khi đã tag xong, bạn chỉ cần "đẩy" nó đi:

```bash
docker push congdev/my-laravel-app:v1.0
```

---

### Mẹo nhỏ cho Workflow của bạn:

#### Cách kiểm tra trước khi push
Để chắc chắn bạn đã tag đúng, hãy dùng lệnh:
```bash
docker images
```
Bạn sẽ thấy một dòng mới xuất hiện với Repository là `congdev/my-laravel-app`.

#### Tối ưu hóa việc Build và Tag cùng lúc
Thay vì build xong rồi mới tag, bạn có thể làm cả hai việc trong một lệnh duy nhất khi đứng ở thư mục có `Dockerfile`:
```bash
docker build -t congdev/my-laravel-app:v1.0 .
```

#### Sử dụng Docker Compose để Push
Nếu bạn đang quản lý dự án bằng `docker-compose.yml`, bạn có thể định nghĩa tên image trực tiếp trong đó:

```yaml
services:
  app:
    build: .
    image: congdev/my-laravel-app:v1.0
```
Sau đó chỉ cần gõ:
```bash
docker compose build
docker compose push
```

---

### Lưu ý về bảo mật (Zero Trust context)
Vì bạn đang dùng **Cloudflare Zero Trust**, hãy nhớ rằng việc push image lên Docker Hub là kết nối **outbound** (đi ra ngoài). Thông thường Cloudflare Tunnel không chặn chiều này trừ khi bạn cấu hình Gateway cực kỳ khắt khe. 

Nếu sau này bạn muốn "kéo" (pull) image này về một server khác cũng chạy Docker, bạn chỉ cần `docker login` trên máy đó và `docker pull congdev/my-laravel-app:v1.0` là xong.

Bạn có định dùng các bản build này để chạy **Multi-stage build** (như dùng Alpine cho nhẹ image) mà bạn thường hay làm không?