# Để tạo một menu có các mục con
## Đăng ký Menu và Submenu (Xổ xuống như Post)
```php
add_action('admin_menu', 'laravel_redis_sync_menu');

function laravel_redis_sync_menu() {
    // 1. Tạo Menu chính (Cái icon ở Sidebar)
    add_menu_page(
        'Laravel Sync',             // Tiêu đề trang
        'Laravel Sync',             // Tên hiển thị ở Sidebar
        'manage_options',           // Quyền hạn (Admin mới thấy)
        'laravel-sync-main',        // Slug (ID duy nhất)
        'render_main_config_page',  // Hàm hiển thị giao diện
        'dashicons-cloud-upload',   // Icon (Cloud)
        80                          // Vị trí ở sidebar
    );

    // 2. Tạo Submenu con: Cấu hình Host (Giống Post -> Add New)
    add_submenu_page(
        'laravel-sync-main',        // Slug của menu cha
        'Redis Settings',           // Tiêu đề trang con
        'Redis Settings',           // Tên hiển thị ở menu con
        'manage_options',
        'laravel-sync-settings',    // Slug riêng của trang con
        'render_redis_settings_page'
    );
}
```
## Giao diện trang Cấu hình (Input/Value cho Host)
```php
// Giao diện trang Redis Settings
function render_redis_settings_page() {
    ?>
    <div class="wrap">
        <h1>Redis Server Configuration</h1>
        <form method="post" action="options.php">
            <?php
            // WordPress sẽ tự động xử lý lưu dữ liệu cho bạn nếu dùng Settings API
            settings_fields('laravel_redis_group');
            do_settings_sections('laravel-sync-settings');
            ?>
            <table class="form-table">
                <tr valign="top">
                    <th scope="row">Redis Host</th>
                    <td>
                        <input type="text" name="redis_host" 
                               value="<?php echo esc_attr(get_option('redis_host', '127.0.0.1')); ?>" 
                               class="regular-text" placeholder="127.0.0.1" />
                    </td>
                </tr>
                <tr valign="top">
                    <th scope="row">Redis Port</th>
                    <td>
                        <input type="number" name="redis_port" 
                               value="<?php echo esc_attr(get_option('redis_port', '6379')); ?>" 
                               class="small-text" />
                    </td>
                </tr>
                <tr valign="top">
                    <th scope="row">Laravel Cache Prefix</th>
                    <td>
                        <input type="text" name="laravel_prefix" 
                               value="<?php echo esc_attr(get_option('laravel_prefix', 'laravel_cache:')); ?>" 
                               class="regular-text" />
                        <p class="description">Phải khớp với REDIS_PREFIX trong file .env của Laravel.</p>
                    </td>
                </tr>
            </table>
            <?php submit_button('Save Settings'); ?>
        </form>
    </div>
    <?php
}

// Đăng ký các field để WordPress tự động lưu vào bảng wp_options
add_action('admin_init', function() {
    register_setting('laravel_redis_group', 'redis_host');
    register_setting('laravel_redis_group', 'redis_port');
    register_setting('laravel_redis_group', 'laravel_prefix');
});
```
## Cách "Nhận thay đổi" tự động để đồng bộ
```php
add_action('save_post', 'sync_redis_on_save', 10, 3);

function sync_redis_on_save($post_id, $post, $update) {
    // Chỉ chạy nếu là bản cập nhật thực sự
    if (!$update || wp_is_post_revision($post_id)) return;

    // Lấy thông tin cấu hình từ trang Settings ở Bước 2
    $host   = get_option('redis_host', '127.0.0.1');
    $port   = get_option('redis_port', '6379');
    $prefix = get_option('laravel_prefix', 'laravel_cache:');

    if (class_exists('Redis')) {
        try {
            $redis = new Redis();
            $redis->connect($host, $port);
            
            // Xóa cache liên quan đến post này bên Laravel
            // Laravel thường lưu cache key kiểu: prefix + tên model hoặc ID
            $redis->del($prefix . 'post_' . $post_id);
            
            // Log lại để kiểm tra
            error_log("Redis Synced: Deleted key " . $prefix . 'post_' . $post_id);
        } catch (Exception $e) {
            error_log("Redis Error: " . $e->getMessage());
        }
    }
}
```

## Code kết hợp mẫu (Dùng Vanilla JS + WP Packages)
```php
import { subscribe, select } from '@wordpress/data';
import apiFetch from '@wordpress/api-fetch';

/**
 * Theo dõi trạng thái lưu của Editor
 */
let isSavingPost = false;

const unsubscribe = subscribe(() => {
    const editor = select('core/editor');
    
    if (!editor) return;

    const wasSaving = isSavingPost;
    isSavingPost = editor.isSavingPost();

    // Điều kiện: Vừa mới kết thúc quá trình lưu (Saving chuyển từ true -> false)
    // Và việc lưu đó phải thành công
    if (wasSaving && !isSavingPost && editor.didPostSaveRequestSucceed()) {
        const postId = editor.getCurrentPostId();
        
        console.log(`Phát hiện lưu thành công Post ID: ${postId}. Đang báo hiệu xóa Redis...`);

        // Thực thi gọi API xóa Redis
        apiFetch({
            path: '/laravel-sync/v1/clear-cache',
            method: 'POST',
            data: { post_id: postId }
        }).then((res) => {
            console.log('Phản hồi từ Server:', res.message);
        }).catch((err) => {
            console.error('Lỗi đồng bộ Redis:', err);
        });
    }
});
```
## Cách Enqueue (nhúng) file này vào Editor

```php
add_action('enqueue_block_editor_assets', function() {
    // Nếu dùng wp-scripts, hãy trỏ vào file build/index.js
    $asset_file = include( plugin_dir_path( __FILE__ ) . 'build/index.asset.php');

    wp_enqueue_script(
        'laravel-sync-js',
        plugins_url( 'build/index.js', __FILE__ ),
        $asset_file['dependencies'], // Sẽ tự động có ['wp-data', 'wp-api-fetch', 'wp-element']
        $asset_file['version']
    );
});
```
## Code thực tế để "bắt" đúng thời điểm
```php
import { subscribe, select } from '@wordpress/data';

let wasSaving = false;

const unsubscribe = subscribe(() => {
    const editor = select('core/editor');
    if (!editor) return;

    const isSaving = editor.isSavingPost();
    const isAutosaving = editor.isAutosavingPost();
    const didSaveSucceed = editor.didPostSaveRequestSucceed();

    // CHỈ CHẠY KHI: 
    // 1. Trạng thái vừa chuyển từ "Đang lưu" sang "Xong" (wasSaving && !isSaving)
    // 2. Không phải là tự động lưu nháp (!isAutosaving)
    // 3. Kết quả lưu phải thành công (didSaveSucceed)
    if (wasSaving && !isSaving && !isAutosaving && didSaveSucceed) {
        console.log("Xác nhận: Dữ liệu đã vào DB. Giờ là lúc xóa Redis!");
        // Gọi apiFetch của bạn ở đây
    }

    wasSaving = isSaving;
});
```