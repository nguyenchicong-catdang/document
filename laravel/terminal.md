# xoa all cache
php artisan optimize:clear
# vendor:publish
php artisan vendor:publish --tag=src-views
# Providers
php artisan config:show app
# tinker
php artisan tinker
# Cách kiểm tra Provider đã nằm trong Container chưa -> vào tinker
app()->getLoadedProviders();
## Giải thích tại sao config:show app trước đó không hiện
Trong Laravel 11/12, mảng providers trong config/app.php chỉ là cấu hình tĩnh. Khi bạn đăng ký động qua $app->register(), bạn đang thêm trực tiếp vào Runtime của Container.

Lệnh config:show app chỉ đọc file cấu hình tĩnh.

Lệnh app()->getLoadedProviders() mới là kết quả thực tế trong Container.
## Cách kiểm tra (Test) xem Binding đã thành công chưa
php artisan tinker

$instance = app(\SrcApp\Contracts\UserRepositoryInterface::class);
echo get_class($instance);

## Test bằng lệnh curl (Artisan Terminal)
curl -I http://127.0.0.1:8000/admin-dashboard

curl -I http://127.0.0.1:8000/admin-dashboard?admin=1

# post
```bash
curl -X POST http://127.0.0.1:8000/api/login-api \
     -H "Content-Type: application/json" \
     -d '{"username": "admin", "password": "your_password"}'
```