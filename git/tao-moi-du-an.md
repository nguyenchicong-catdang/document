# cd cd ~/openLiteSpeed/VD1
```bash
cd ~/openLiteSpeed/VD1
pwd
```
# cai dat
```bash
git init
```
## Cách cấu hình tên branch mặc định toàn cục (để lần sau git init sẽ dùng tên bạn chọn, ví dụ: main):
```bash
git config --global init.defaultBranch main
```
### Cách đổi tên branch vừa tạo (từ master sang tên khác, ví dụ: main):
```bash
git branch -m main
```
# remote add
## khởi tạo remote
```bash
git remote add origin git@github.com:nguyenchicong-catdang/openLiteSpeed.git
```
# Thêm các tệp của bạn (Stage):
```bash
git add .
```
# Tạo commit đầu tiên (Commit):
```bash
git commit -m "Initial commit"
```
## comit and push
```bash
git push -u origin main
```
### Gỡ bỏ Submodule (Nếu có): Nếu nó là Submodule, bạn cần chạy lệnh:

```bash
cd ~/openLiteSpeed/VD1
rm -rf .git
# tham khao
git submodule deinit VD1
git rm VD1
cd ~/openLiteSpeed/VD1
rm -rf .git
```