# ~/scripts/learn-api.sh
```bash
nano ~/scripts/learn-api.sh
```
# Cài đặt công cụ hỗ trợ expect
```bash
#!/usr/bin/expect -f
# Đặt biến mật khẩu (Thay 'your_password' bằng mật khẩu thật của bạn)
set pass "your pass"
# Thiết lập thời gian chờ (giây)
set timeout 10
# 1. Khởi động bash
spawn bash
# 2. Chờ cho đến khi bash load xong hoàn toàn (nhận diện dấu $ ở cuối)
# Dùng "*$ " nghĩa là chấp nhận mọi ký tự phía trước, miễn kết thúc là $
#expect -re ".*\\$ "
# 2. Chờ dấu nhắc lệnh ($) hiện ra rồi gửi lệnh cd
expect "$ "
# send "sudo cd ~/openLiteSpeed && ls\r"
# 3. Gửi lệnh (Sửa lại: CD trước, sau đó sudo LS)
send "cd ~\r"
# kill port
expect "$ "
#send "sudo killall -9 php8.3\r"
#send "sudo killall -9 php8.3; sudo killall -9 node\r"
# Đúng: dùng send, dấu ngoặc kép bao quanh lệnh, lệnh kết thúc bằng \r
send "sudo pkill -9 -f 'php|artisan|composer|node'\r"
# xử lý mật khẩu
# 5. Xử lý mật khẩu sudo
expect {
    "*password for*" {
        send "$pass\r"
        exp_continue
    }
     "$ "
}
# 3. Chờ dấu nhắc lệnh hiện ra rồi gửi lệnh sudo
# expect "$ "
send "code ~/ws/ws.code-workspace\r"
# Tự động chạy cụm lệnh bash bên trong môi trường expect
# spawn bash -c "cd ~ && ls; exec bash"
# xoa port laravel + vite dev
expect "$ "
send "sudo fuser -k 8000/tcp; sudo fuser -k 5173/tcp\r"
# chay composer
expect "$ "
send "cd ~/openLiteSpeed/learn-api && composer run dev\r"
# Giữ tương tác để bạn có thể tiếp tục gõ lệnh
interact
```
# run window terminal
```bash
wsl.exe -d Ubuntu-24.04 -e expect ~/scripts/learn-api.sh
```
