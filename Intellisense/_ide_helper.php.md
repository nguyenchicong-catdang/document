# _ide_helper.php

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

# use plugin
```php
// Cách đúng cho WordPress Plugin
if (class_exists('\Redis')) { 
    $redis = new \Redis();
    $redis->connect('127.0.0.1', 6379);
}

// Cách đúng cho WordPress Plugin

function notify_redis_on_sidebar_update()
{
    if (class_exists('Redis')) {
        try {
            /** @var Redis $redis */
            $redis = new Redis();
            $redis->connect('127.0.0.1', 6379);
            // Xóa key cache của sidebar bên Laravel
            $redis->del('laravel_cache:sidebar_data');
        } catch (Exception $e) {
            // Log lỗi nếu không kết nối được Redis
        }
    }
}

```



> tham khao :  // composer require --dev php-stubs/redis-stubs

