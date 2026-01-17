# Data Store của WordPress.
## Cách bắt sự kiện "Save" thành công từ React:

```php
import { subscribe, select } from '@wordpress/data';

// Đăng ký một hàm theo dõi sự thay đổi của Store
const unsubscribe = subscribe(() => {
    const isSaving = select('core/editor').isSavingPost();
    const isSaveSuccess = select('core/editor').didPostSaveRequestSucceed();

    if (isSaveSuccess && !isSaving) {
        console.log('WP vừa Save thành công! Chạy lệnh xóa Redis thôi...');
        // Gọi hàm xóa Redis của bạn ở đây
    }
});
```

# Tương tác với Plugin (Gửi dữ liệu lên Server)
```js
import apiFetch from '@wordpress/api-fetch';

const clearRedis = async () => {
    try {
        const result = await apiFetch({
            path: '/wp/v2/posts', // Bạn có thể tạo Custom Route API riêng
            method: 'POST',
            data: { action: 'clear_cache' },
        });
        alert('Đã đồng bộ Redis!');
    } catch (error) {
        console.error(error);
    }
};
```
## Lắng nghe qua Action Hook (Phía PHP)
```php
// Trong plugin-main.php
add_action('save_post', 'my_sync_redis_logic', 10, 3);

function my_sync_redis_logic($post_id, $post, $update) {
    // Nếu không phải là bản cập nhật thì bỏ qua
    if (!$update) return;

    // Tại đây bạn thực hiện lệnh PHP-Redis
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);
    $redis->publish('wp-updates', 'clear_now'); 
}
```