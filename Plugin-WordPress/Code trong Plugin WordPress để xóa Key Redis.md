# Code trong Plugin WordPress để xóa Key Redis

```php
// Hàm dùng chung để thao tác Redis
function wp_redis_direct_action($action = 'clear_all') {
    if (!class_exists('Redis')) {
        return false; // Server chưa cài extension redis
    }

    try {
        $redis = new Redis();
        $redis->connect('127.0.0.1', 6379);
        
        // Nếu Redis có mật khẩu: $redis->auth('password');

        if ($action == 'clear_all') {
            // Cách 1: Xóa sạch (Cẩn thận nếu dùng chung Redis cho việc khác)
            // $redis->flushDB(); 

            // Cách 2: Xóa theo Prefix mà Laravel/Corcel đang dùng
            // Giả sử Laravel cache có prefix là 'laravel_cache:'
            $keys = $redis->keys('laravel_cache:*');
            foreach ($keys as $key) {
                $redis->del($key);
            }
        }
        
        $redis->close();
        return true;
    } catch (Exception $e) {
        return false;
    }
}

// Hook khi nhấn nút Ajax từ jQuery (như đã nói ở phần trước)
add_action('wp_ajax_manual_clear_cache', function() {
    $success = wp_redis_direct_action();
    if ($success) {
        wp_send_json_success(array('message' => 'Đã xóa trực tiếp trong Redis!'));
    } else {
        wp_send_json_error(array('message' => 'Lỗi kết nối Redis.'));
    }
});
```
# Sử dụng Redis Pub/Sub (Nâng cao)
```php
function wp_redis_publish_update($message) {
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);
    
    // Gửi một tin nhắn vào kênh 'wp-updates'
    // Phía Laravel hoặc một Script khác sẽ "nghe" kênh này để xử lý
    $redis->publish('wp-updates', json_encode([
        'event' => 'template_updated',
        'data' => $message
    ]));
    
    $redis->close();
}
```

## Thay jQuery bằng Vanilla JavaScript
```php
document.addEventListener('DOMContentLoaded', function() {
    const syncBtn = document.getElementById('sync-now-btn');
    const resultDiv = document.getElementById('sync-result');

    if (syncBtn) {
        syncBtn.addEventListener('click', function() {
            syncBtn.disabled = true;
            syncBtn.innerText = 'Sending...';

            // Gọi đến admin-ajax.php của WordPress
            fetch('<?php echo admin_url('admin-ajax.php?action=manual_clear_cache'); ?>')
                .then(response => response.json())
                .then(data => {
                    resultDiv.innerHTML = `<div class="updated"><p>Kết quả: ${data.data.message}</p></div>`;
                    syncBtn.disabled = false;
                    syncBtn.innerText = 'Clear Laravel Cache Now';
                })
                .catch(error => {
                    console.error('Error:', error);
                    syncBtn.disabled = false;
                });
        });
    }
});
```
## Luồng đi: Browser -> Action Hook -> PHP Logic
```php
// Hook này dành cho người dùng đã đăng nhập (Admin)
add_action('wp_ajax_my_redis_sync', 'handle_redis_sync_logic');

function handle_redis_sync_logic() {
    // ĐÂY LÀ NƠI CHẠY PHP THUẦN
    // 1. Kết nối Redis
    // 2. Xóa Key/Flush
    // 3. Trả về kết quả JSON
    
    $success = do_something_with_redis(); // Hàm PHP bạn viết

    wp_send_json_success(['message' => 'Redis đã được dọn dẹp!']);
}
```