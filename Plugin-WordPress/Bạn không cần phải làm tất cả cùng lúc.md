# Bạn không cần phải làm tất cả cùng lúc
## Cấp độ 1: Chỉ dùng PHP Hook (Dễ nhất - 15 phút)
```php
// Chỉ cần file này là đủ chạy
add_action('save_post', function($post_id) {
    if (wp_is_post_revision($post_id)) return;
    
    // Kết nối Redis và xóa key
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);
    $redis->del('laravel_cache:post_' . $post_id);
});
```
## Cấp độ 2: Thêm Menu Dashboard (Trung bình)

> _ide_helper.php

```php
<?php
/**
 * File này chỉ để VS Code nhận diện class Redis, không chạy thực tế.
 */
class Redis {
    public function connect($host, $port = 6379, $timeout = 0.0) {}
    public function del($key) {}
    public function set($key, $value) {}
    public function get($key) {}
    // Thêm các hàm khác nếu bạn cần
}
```