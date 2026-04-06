Chào bạn, việc code trực tiếp trên server qua Cloudflare Zero Trust (thường là qua Cloudflare Tunnel) là một giải pháp rất bảo mật vì bạn không cần mở port 22 (SSH) ra ngoài internet.

Để thực hiện việc này từ máy Windows (sử dụng VS Code và WSL), bạn cần thiết lập một "đường ống" SSH đi qua công cụ `cloudflared`. Dưới đây là các bước chi tiết:

---

### 1. Cài đặt `cloudflared` trên máy khách (Windows)
Trước tiên, máy tính bạn đang dùng để code cần có công cụ của Cloudflare để giải mã kết nối.

* Tải file `.msi` hoặc `.exe` của **cloudflared** từ [trang chủ Cloudflare](https://github.com/cloudflare/cloudflared/releases).
* Sau khi cài đặt, hãy mở PowerShell và gõ `cloudflared --version` để đảm bảo nó đã hoạt động.

### 2. Cấu hình File SSH Config trên Windows
VS Code Remote SSH sẽ sử dụng file cấu hình SSH của Windows để biết cách kết nối qua Cloudflare.

1.  Mở VS Code.
2.  Nhấn `Ctrl + Shift + P`, gõ **"Open SSH Configuration File"** và chọn file tại đường dẫn `C:\Users\Tên_User\.ssh\config`.
3.  Thêm đoạn cấu hình sau vào file:

```text
Host server-biet-danh
    HostName cua-so-cua-ban.com
    User username-tren-server
    ProxyCommand C:\bin\cloudflared.exe access ssh --hostname %h
```

> **Lưu ý:** > * `HostName`: Là domain bạn đã cấu hình trong Cloudflare Tunnel (ví dụ: `dev.yourdomain.com`).
> * `ProxyCommand`: Hãy trỏ đúng đường dẫn đến file `cloudflared.exe` bạn vừa tải.
> * `User`: Tên đăng nhập của bạn trên server Linux (ví dụ: `root` hoặc `ubuntu`).

### 3. Kết nối từ VS Code
1.  Tại thanh Activity Bar bên trái VS Code, chọn biểu tượng **Remote Explorer** (hình màn hình máy tính).
2.  Bạn sẽ thấy mục `server-biet-danh` xuất hiện. Chuột phải và chọn **Connect in Current Window**.
3.  Một cửa sổ trình duyệt có thể hiện ra yêu cầu bạn đăng nhập vào hệ thống Zero Trust của tổ chức bạn. Sau khi đăng nhập thành công, VS Code sẽ tự động kết nối SSH qua tunnel.

---

### 4. Cách sử dụng từ WSL (Tùy chọn)
Nếu bạn muốn SSH vào server đó **ngay từ bên trong terminal của WSL**, bạn cần cài đặt `cloudflared` phiên bản Linux (deb/rpm) vào WSL, sau đó cấu hình file `~/.ssh/config` bên trong WSL tương tự như trên:

```bash
Host server-biet-danh
    HostName cua-so-cua-ban.com
    User username-tren-server
    ProxyCommand /usr/local/bin/cloudflared access ssh --hostname %h
```

### Một số lưu ý quan trọng:
* **SSH Key:** Để trải nghiệm mượt mà nhất và không phải nhập mật khẩu server nhiều lần, bạn nên copy **Public Key** của máy Windows/WSL vào file `~/.ssh/authorized_keys` trên server.
* **Policy:** Đảm bảo trong bảng điều khiển Cloudflare Zero Trust, bạn đã tạo một **Access Policy** cho phép email hoặc thiết bị của bạn được quyền truy cập vào ứng dụng SSH đó.
* **Hiệu năng:** Vì code qua Tunnel, tốc độ phản hồi sẽ phụ thuộc vào đường truyền quốc tế. Tuy nhiên, với VS Code Remote SSH, các thao tác gõ phím thường rất mượt vì nó có cơ chế dự đoán và cache tốt.

Chúc bạn thiết lập thành công! Nếu gặp lỗi `ProxyCommand` không tìm thấy file, hãy kiểm tra lại đường dẫn tuyệt đối đến `cloudflared.exe`.