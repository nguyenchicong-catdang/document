## ngăn chặn tấn công => trước khi ghi vào db
```php
<?php
// Tên action 'save_laravel_sidebar_content' phải khớp với input hidden trong form JS
add_action('admin_post_save_laravel_sidebar_content', function () {

    // 1. Kiểm tra quyền hạn
    if (!current_user_can('manage_options')) {
        wp_die('Bạn không có quyền truy cập.');
    }
    // chống lại các cuộc tấn công giả mạo (CSRF)
    if (!isset($_POST['laravel_sidebar_nonce']) || !wp_verify_nonce($_POST['laravel_sidebar_nonce'], 'save_sidebar_action')) {
        wp_die('Hết phiên làm việc, vui lòng thử lại.');
    }

    // 2. Lấy và xử lý dữ liệu (Upsert)
    // Trong file xử lý lưu dữ liệu của bạn
    if (isset($_POST['laravel_sidebar_html'])) {
        $raw_html = wp_unslash($_POST['laravel_sidebar_html']);

        // Bước 1: Loại bỏ hoàn toàn thẻ <script> và nội dung bên trong nó
        $clean_html = preg_replace('#<script(.*?)>(.*?)</script>#is', '', $raw_html);

        // Bước 2: Loại bỏ các thuộc tính sự kiện nguy hiểm (onerror, onclick, onload, v.v.)
        // Đây là cách chặn mã độc như <img src="x" onerror="alert(1)">
        $clean_html = preg_replace('#\son[a-z]+=["\'].*?["\']#is', '', $clean_html);

        // Bước 3: Loại bỏ các giao thức javascript: trong thuộc tính href
        $clean_html = preg_replace('#href=["\']javascript:.*?["\']#is', 'href="#"', $clean_html);

        // Bước 4: Sử dụng wp_kses_post để làm sạch theo chuẩn WordPress
        $safe_html = wp_kses_post($clean_html);

        // Lưu vào database
        update_option('sidebar_html_content', $safe_html);
    }

    // 3. Điều hướng quay lại trang chính kèm tham số thông báo
    wp_redirect(admin_url('admin.php?page=laravel-sidebar&status=updated'));
    exit;
});

```
## lọc đầu ra
```php
<?php

namespace Wp\Sidebars\Actions;

// use Illuminate\Support\Collection;
use Wp\Sidebars\Sidebar;

class SidebarGetData
{
    public static function toArray(Sidebar $sidebar): array
    {
        $optionsName = 'sidebar_html_content';
        //https://github.com/corcel/corcel
        // $siteUrl = Option::get('siteurl');
        $data = $sidebar::get($optionsName);
        // Trong SidebarGetData.php
        $data = strip_tags($data, '<ul><li><a>'); // Chỉ cho phép các thẻ HTML an toàn.
        return [
            'content' => $data,
        ];
    }
}
```