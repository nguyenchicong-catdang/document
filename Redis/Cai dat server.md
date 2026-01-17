# Cài đặt Redis Server và Tools

> sudo apt install redis-server

> sudo service redis-server start

> redis-cli ping

> sudo apt install php8.3-redis

> CACHE_STORE=redis

## lưu ý
### Tại môi trường Dev (có Redis):
> CACHE_STORE=redis

> CACHE_STORE=database

> CACHE_STORE=file

Dùng Database: Nếu bạn muốn dữ liệu tập trung mà không có Redis, hãy dùng database. (Lưu ý: Bạn phải chạy php artisan cache:table và php artisan migrate để tạo bảng lưu trữ).