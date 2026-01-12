# Cách tiếp cận tốt hơn: Corcel
> composer require jgrossi/corcel

# Cấu hình Database Trong file config/database.php
```php
'connections' => [
    'wordpress' => [
        'driver' => 'mysql',
        'host' => env('DB_HOST', '127.0.0.1'),
        'database' => 'your_wp_database',
        'username' => 'your_wp_user',
        'password' => 'your_wp_password',
        'prefix' => 'wp_', // Prefix mặc định của WP
    ],
]
```
# Corcel biến tất cả đống phức tạp đó thành Eloquent thuần túy:
```php
use Corcel\Model\Post;

// Lấy tất cả bài viết trạng thái 'publish'
$posts = Post::published()->get();

// Lấy 1 bài viết kèm theo ảnh đại diện và các meta data
$post = Post::slug('my-post-slug')->with('taxonomies')->first();
$imageUrl = $post->thumbnail; // Tự động lấy URL ảnh từ bảng meta
```

## Xử lý Post Meta (Custom Fields)
```php
$seoTitle = $post->meta->_yoast_wpseo_title; // Lấy dữ liệu từ plugin SEO
$price = $post->meta->product_price; // Lấy từ Advanced Custom Fields (ACF)
```

## Xử lý Phân loại (Taxonomies/Categories)
> $categories = $post->taxonomies()->where('taxonomy', 'category')->get();

## Lấy Menu của WordPress
```php
use Corcel\Model\Menu;

$mainMenu = Menu::slug('main-menu')->first();
foreach ($mainMenu->items as $item) {
    echo $item->instance()->title; // In ra tên menu
}
```

## Vấn đề cần lưu ý: Đường dẫn ảnh (Media)
```php
// Ví dụ trong Laravel Model hoặc Resource
public function getFullImageUrlAttribute() {
    return 'https://admin.website.com' . $this->thumbnail;
}
```

## Xử lý nội dung dạng Block (Gutenberg)

> Nếu Laravel render HTML: Bạn chỉ cần in ra {!! $post->post_content !!}. CSS của bạn ở Laravel cần tương thích với các class của WP (ví dụ: wp-block-image).

## Nếu dùng React (Inertia/SPA)
> Nếu dùng React (Inertia/SPA): Bạn có thể gửi chuỗi HTML này về và dùng dangerouslySetInnerHTML.

## ách biệt PHP-FPM Pool (Cách hiệu quả nhất)
> Pool Laravel: Cấu hình pm = dynamic với số lượng worker lớn để phản hồi khách hàng ngay lập tức.

> Pool WordPress: Cấu hình pm = ondemand.

```Ini, TOML
[wordpress]
user = www-data
group = www-data
listen = /var/run/php/php-wp.sock
pm = ondemand
pm.max_children = 5
pm.process_idle_timeout = 10s ; Giải phóng worker sau 10 giây không dùng
```

# Giới hạn RAM cho WordPress (Memory Limit)
```php
define('WP_MEMORY_LIMIT', '64M'); // Giới hạn cho người dùng ngoài (thực tế mình dùng Laravel rồi nên cái này không quan trọng)
define('WP_MAX_MEMORY_LIMIT', '128M'); // Giới hạn cho Admin
```

# Cấu hình OpCache thông minh
> Mẹo: Nếu RAM quá hẹp, bạn có thể sử dụng thuộc tính opcache.blacklist_filename để yêu cầu PHP không cache các file của WordPress.

# Xử lý Database (MySQL)
Hạn chế WP Heartbeat: WordPress mặc định cứ 15-60 giây lại gửi một request ngầm để kiểm tra thông báo. Hãy cài plugin "Heartbeat Control" để tắt nó đi hoặc tăng thời gian lên 5 phút. Điều này giúp database không phải xử lý các câu lệnh vô ích từ admin khi bạn chỉ đang để tab admin đó mà không làm gì.

Cấu hình Nginx để điều hướng
```Nginx
# Laravel Front-end
server {
    server_name website.com;
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php-laravel.sock; # Pool mạnh
        ...
    }
}

# WordPress Admin
server {
    server_name admin.website.com;
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php-wp.sock; # Pool "ondemand" tiết kiệm RAM
        ...
    }
}
```