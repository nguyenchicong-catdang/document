# list port
```bash
sudo ss -tulpn | grep LISTEN
```
# expect sudo
```bash
#!/usr/bin/expect -f

# Đặt biến mật khẩu (Thay 'your_password' bằng mật khẩu thật của bạn)
set pass "your_password"

# Thiết lập thời gian chờ (giây)
set timeout 10

# 1. Khởi động bash
spawn bash

# 2. Chờ dấu nhắc lệnh ($) hiện ra rồi gửi lệnh cd
expect "$ "
send "cd ~/scripts\r"

# 3. Chờ dấu nhắc lệnh hiện ra rồi gửi lệnh sudo
expect "$ "
send "sudo ls -la /root\r"
send "sudo killall -9 php8.3; sudo killall -9 node\r"
# Đúng: dùng send, dấu ngoặc kép bao quanh lệnh, lệnh kết thúc bằng \r
send "sudo pkill -9 -f 'php|artisan|composer|node'\r"
# 4. Xử lý khi hệ thống hỏi mật khẩu sudo
expect {
    "password for*" {
        send "$pass\r"
        exp_continue
    }
    "$ " {
        # Nếu đã vào được dấu nhắc lệnh (đã xong sudo)
    }
}

# 5. Thực hiện thêm lệnh khác nếu muốn
send "echo 'Da vao duoc quyen root'\r"

# 6. Giữ lại tương tác để bạn tiếp tục gõ
interact
```

# expect sudo v2
```bash
#!/usr/bin/expect -f

set timeout 10
set pass "your_password"

# 1. Khởi động bash
spawn bash

# 2. Chờ cho đến khi bash load xong hoàn toàn (nhận diện dấu $ ở cuối)
# Dùng "*$ " nghĩa là chấp nhận mọi ký tự phía trước, miễn kết thúc là $
expect -re ".*\\$ " 

# 3. Gửi lệnh cd và quan trọng là phải có \r (Enter)
send "cd ~/scripts\r"

# 4. Chờ tiếp dấu $ sau khi đã cd xong để chạy lệnh tiếp theo (ví dụ sudo)
expect -re ".*\\$ "
send "sudo ls\r"

# 5. Xử lý mật khẩu
expect {
    "*password*" {
        send "$pass\r"
        exp_continue
    }
    -re ".*\\$ " {
        # Đã xong
    }
}

interact
```