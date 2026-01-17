# Cách tạo Custom Route riêng cho Plugin của bạn (Giải pháp chuẩn)
## Đăng ký Route trong PHP (plugin-main.php)
```php
add_action('rest_api_init', function () {
    register_rest_route('laravel-sync/v1', '/clear-cache', array(
        'methods' => 'POST',
        'callback' => 'handle_redis_clear_request',
        'permission_callback' => function () {
            // Chỉ cho phép người dùng có quyền quản lý options mới được gọi
            return current_user_can('manage_options');
        }
    ));
});

function handle_redis_clear_request($request) {
    // PHP-Redis Logic ở đây
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);
    $redis->flushDB(); // Hoặc xóa key theo prefix Laravel

    return new WP_REST_Response(['message' => 'Redis cleared!'], 200);
}
```

## Gọi từ React (Sử dụng apiFetch)
```js
const clearRedis = async () => {
    try {
        const result = await apiFetch({
            path: '/laravel-sync/v1/clear-cache', // Đường dẫn bạn vừa tạo ở trên
            method: 'POST'
        });
        alert(result.message);
    } catch (error) {
        console.error("Lỗi rồi:", error);
    }
};
```