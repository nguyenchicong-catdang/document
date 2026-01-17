# Cấu trúc một Plugin WordPress
> Đường dẫn: /wp-content/plugins/laravel-sync-cache/laravel-sync-cache.php

```php
<?php
/*
Plugin Name: Laravel Cache Sync Tool
Description: Gửi Webhook sang Laravel để xóa Redis cache khi update giao diện.
Version: 1.0
Author: Your Name
*/

// Ngăn chặn truy cập trực tiếp vào file
if (!defined('ABSPATH')) exit;
```

# Thêm Menu/Button vào Sidebar Admin (Tạo sự tự do)
```php
// 1. Khai báo menu trong sidebar
add_action('admin_menu', function() {
    add_menu_page(
        'Laravel Cache',         // Tiêu đề trang
        'Sync Laravel',          // Tên hiển thị ở Sidebar
        'manage_options',        // Quyền truy cập
        'laravel-cache-sync',    // Slug (ID của trang)
        'render_sync_page',      // Hàm hiển thị nội dung
        'dashicons-update',      // Icon
        100                      // Vị trí
    );
});

// 2. Nội dung trang quản lý và Nút bấm
function render_sync_page() {
    ?>
    <div class="wrap">
        <h1>Laravel Cache Management</h1>
        <p>Nhấn vào nút dưới đây để xóa sạch cache bên phía Laravel Frontend.</p>
        <button id="sync-now-btn" class="button button-primary">Clear Laravel Cache Now</button>
        <div id="sync-result" style="margin-top: 20px;"></div>
    </div>

    <script>
    jQuery(document).ready(function($) {
        $('#sync-now-btn').on('click', function() {
            $(this).prop('disabled', true).text('Sending...');
            
            // Sử dụng Fetch API hoặc Ajax để gửi tín hiệu
            fetch('<?php echo admin_url('admin-ajax.php?action=manual_clear_cache'); ?>')
                .then(response => response.json())
                .then(data => {
                    $('#sync-result').html('<div class="updated"><p>Kết quả: ' + data.message + '</p></div>');
                    $('#sync-now-btn').prop('disabled', false).text('Clear Laravel Cache Now');
                });
        });
    });
    </script>
    <?php
}
```

# Xử lý logic gửi Webhook (Fetch hay Submit Form?)
```php
// Hàm gửi Webhook sang Laravel
function send_clear_cache_webhook($reason = 'manual') {
    $laravel_url = 'https://your-laravel-site.com/api/clear-cache';
    $secret_key = 'your_secret_token'; // Bảo mật API

    $response = wp_remote_post($laravel_url, array(
        'method'    => 'POST',
        'headers'   => array('Content-Type' => 'application/json'),
        'body'      => json_encode(array(
            'action' => 'clear_cache',
            'reason' => $reason,
            'key'    => $secret_key
        )),
        'timeout'   => 15,
    ));

    return $response;
}

// Xử lý Ajax khi nhấn nút thủ công ở Bước 2
add_action('wp_ajax_manual_clear_cache', function() {
    send_clear_cache_webhook('manual_button_click');
    wp_send_json_success(array('message' => 'Đã gửi yêu cầu xóa cache sang Laravel!'));
});
```

# Tự động hóa với Hooks (Action)
```php
add_action('wp_after_insert_post', function($post_id, $post, $update) {
    // Chỉ chạy nếu là update và là template của giao diện
    if ($update && ($post->post_type == 'wp_template' || $post->post_type == 'wp_template_part')) {
        send_clear_cache_webhook('template_updated: ' . $post->post_name);
    }
}, 10, 3);
```

# Cách xử lý phía Laravel để nhận Webhook an toàn
```php
use Illuminate\Support\Facades\Cache;
use Illuminate\Http\Request;

Route::post('/clear-cache', function (Request $request) {
    $secretToken = 'your_secret_token'; // Trùng với bên WP
    
    if ($request->input('key') !== $secretToken) {
        return response()->json(['message' => 'Unauthenticated'], 401);
    }

    // Xử lý xóa Redis cache
    // Bạn có thể xóa toàn bộ hoặc xóa theo Tag nếu dùng Redis Tagging
    Cache::flush(); 
    
    return response()->json([
        'status' => 'success',
        'message' => 'Redis cache cleared by WordPress',
        'reason' => $request->input('reason')
    ]);
});
```
# Nếu bạn vẫn muốn dùng Fetch JS (Client-side)
```php
'allowed_origins' => ['https://admin.yourdomain.com'],
'allowed_methods' => ['POST', 'GET'],
```