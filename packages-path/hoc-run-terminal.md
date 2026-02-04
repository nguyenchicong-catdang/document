# 1. Tìm và diệt các tiến trình đang chiếm port 8000 và 8080 trước
echo "Đang dọn dẹp các port cũ..."
fuser -k 8000/tcp > /dev/null 2>&1
fuser -k 8080/tcp > /dev/null 2>&1

# 2. Chạy ngầm Laravel
cd "$RUN_LARAVEL"
nohup php artisan serve --port=8000 > /dev/null 2>&1 &

# 3. Chạy ngầm WordPress
cd "$WP_APP_PATH"
nohup wp server --port=8080 > /dev/null 2>&1 &

echo "Tất cả server đã được chạy ngầm. Bạn có thể đóng Terminal này!"