# thm khao huy dang ky
```php
<?php


/*
Plugin Name: Laravel Sidebar Manager
Description: Tùy chỉnh Sidebar HTML và URL cho Laravel Frontend.
Version: 1.0
Author: Your Name
*/

// Ngăn truy cập trực tiếp
if (!defined('ABSPATH')) exit;

// 1. Tạo Menu Sidebar trong WP-Admin
add_action('admin_menu', function () {
    add_menu_page(
        'Laravel Sidebar',          // Tiêu đề trang
        'Laravel Sidebar',          // Tên hiển thị Menu
        'manage_options',           // Quyền admin
        'laravel-sidebar-settings', // Slug
        'render_sidebar_settings',  // Hàm hiển thị giao diện
        'dashicons-layout',         // Icon
        25                          // Vị trí
    );
});

// 2. Đăng ký các trường dữ liệu để WP tự động lưu
add_action('admin_init', function () {
    register_setting('sidebar_group', 'sidebar_name');
    register_setting('sidebar_group', 'sidebar_url');
    register_setting('sidebar_group', 'sidebar_html_content');
});

// 3. Giao diện nhập liệu (HTML/Input)
function render_sidebar_settings()
{
?>
    <div class="wrap">
        <h1>Quản lý Sidebar cho Laravel</h1>
        <form method="post" action="options.php">
            <?php
            settings_fields('sidebar_group');
            do_settings_sections('sidebar_group');
            ?>
            <table class="form-table">
                <tr>
                    <th scope="row">Tên Sidebar</th>
                    <td>
                        <input type="text" name="sidebar_name"
                            value="<?php echo esc_attr(get_option('sidebar_name')); ?>"
                            class="regular-text" />
                    </td>
                </tr>
                <tr>
                    <th scope="row">URL Liên kết</th>
                    <td>
                        <!-- <input type="url" name="sidebar_url" -->
                         <!-- Xử lý text url -->
                            <input type="text" name="sidebar_url"

                            value="<?php echo esc_attr(get_option('sidebar_url')); ?>"
                            class="regular-text" />
                    </td>
                </tr>
                <tr>
                    <th scope="row">HTML Code (Nguyên khối Sidebar)</th>
                    <td>
                        <textarea name="sidebar_html_content" rows="15" cols="50"
                            class="large-text"
                            style="font-family: monospace;"><?php echo esc_textarea(get_option('sidebar_html_content')); ?></textarea>
                        <p class="description">Dán mã HTML Sidebar của bạn vào đây (Div, Ul, Li...).</p>
                    </td>
                </tr>
            </table>
            <?php submit_button('Lưu thay đổi'); ?>
        </form>
    </div>
<?php



}

// 4. [Quan trọng] Tự động xóa Redis khi nhấn Lưu
add_action('update_option_sidebar_html_content', 'notify_redis_on_sidebar_update');
add_action('update_option_sidebar_name', 'notify_redis_on_sidebar_update');


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

// Hàm thực hiện xóa dữ liệu
function laravel_sidebar_cleanup_options()
{
    delete_option('sidebar_name');
    delete_option('sidebar_url');
    delete_option('sidebar_html_content');

    // // Nếu muốn xóa luôn cache Redis khi gỡ plugin
    if (function_exists('notify_redis_on_sidebar_update')) {
        notify_redis_on_sidebar_update();
    }
}

// Đăng ký hook khi Deactivate
register_deactivation_hook(__FILE__, 'laravel_sidebar_cleanup_options');

/**
 * File này chỉ để VS Code nhận diện class Redis, không chạy thực tế.
 * Cách xóa khi "Uninstall" (Xóa hẳn Plugin - Khuyên dùng)
 
<?php
// Nếu không phải lệnh uninstall từ WordPress thì thoát
if (!defined('WP_UNINSTALL_PLUGIN')) {
    exit;
}

// Xóa các key trong bảng wp_options
delete_option('sidebar_name');
delete_option('sidebar_url');
delete_option('sidebar_html_content');

// (Tùy chọn) Xóa sạch trong database nếu bạn dùng nhiều options hơn
// global $wpdb;
// $wpdb->query("DELETE FROM {$wpdb->options} WHERE option_name LIKE 'sidebar_%'");

 */

```