## Giải pháp A: Tạo "Must-Use Plugin" (Khuyên dùng)

## wp-content/mu-plugins/laravel-cache-purger.php

```php
<?php
/*
  Plugin Name: Laravel Cache Purger
  Description: Tự động báo cho Laravel xóa cache khi có bài viết thay đổi.
*/

add_action('save_post', function($post_id, $post, $update) {
    // Chỉ gửi khi bài viết ở trạng thái 'publish'
    if ($post->post_status != 'publish') return;

    // Tránh gửi khi WordPress tự động lưu bản nháp
    if (defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) return;

    $laravel_webhook_url = 'https://website.com/api/webhook/clear-cache';

    wp_remote_post($laravel_webhook_url, [
        'method'    => 'POST',
        'blocking'  => false, // Không bắt Admin phải đợi Laravel phản hồi, giúp lưu bài nhanh hơn
        'body'      => [
            'post_id' => $post_id,
            'slug'    => $post->post_name,
            'secret'  => 'your_very_secure_secret_token'
        ],
    ]);
}, 10, 3);
```