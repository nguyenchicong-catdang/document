# Bạn có thể xem toàn bộ mã nguồn tại GitHub: jgrossi/corcel
>Bạn có thể xem toàn bộ mã nguồn tại GitHub: jgrossi/corcel

## Xóa bỏ rào cản giữa WordPress và Laravel

> $posts = Post::published()->type('product')->take(10)->get();

## Cách viết Accessor để xử lý Media (Ảnh)

```php
namespace App\Models;

use Corcel\Model\Post as CorcelPost;

class Post extends CorcelPost
{
    // Ép Model này luôn dùng kết nối 'wordpress' đã cấu hình ở trên
    protected $connection = 'wordpress';

    /**
     * Accessor cho Featured Image
     * Cách dùng trong Blade: <img src="{{ $post->full_featured_image }}">
     */
    public function getFullFeaturedImageAttribute()
    {
        $url = $this->thumbnail; // Lấy URL gốc từ Corcel

        if (empty($url)) return 'path/to/default-image.jpg';

        // Kiểm tra nếu url chưa có domain thì nối thêm domain của WP Admin vào
        if (!str_starts_with($url, 'http')) {
            return 'https://admin.website.com' . $url;
        }

        return $url;
    }
}
```

## Xử lý nội dung Gutenberg (Blocks) trong Blade SSR

> Khi dùng Blade để render nội dung từ WordPress, bạn cần lưu ý về CSS. Gutenberg sinh ra các class như .wp-block-columns, .wp-block-image.

# Cách cấu hình để Laravel "trỏ" vào WordPress
```php
'connections' => [
    // Kết nối mặc định cho các bảng của Laravel (User, Jobs,...)
    'mysql' => [
        'driver' => 'mysql',
        'database' => 'my_shared_db',
        'prefix' => '', // Laravel không dùng prefix
        // ... các thông số khác
    ],

    // Kết nối dành riêng cho Corcel đọc WordPress
    'wordpress' => [
        'driver' => 'mysql',
        'database' => 'my_shared_db',
        'prefix' => 'wp_', // Ép tất cả truy vấn qua đây phải có wp_
        // ... các thông số khác
    ],
],
```

## Cách viết Accessor để xử lý Media (Ảnh)
```php
namespace App\Models;

use Corcel\Model\Post as CorcelPost;

class Post extends CorcelPost
{
    // Ép Model này luôn dùng kết nối 'wordpress' đã cấu hình ở trên
    protected $connection = 'wordpress';

    /**
     * Accessor cho Featured Image
     * Cách dùng trong Blade: <img src="{{ $post->full_featured_image }}">
     */
    public function getFullFeaturedImageAttribute()
    {
        $url = $this->thumbnail; // Lấy URL gốc từ Corcel

        if (empty($url)) return 'path/to/default-image.jpg';

        // Kiểm tra nếu url chưa có domain thì nối thêm domain của WP Admin vào
        if (!str_starts_with($url, 'http')) {
            return 'https://admin.website.com' . $url;
        }

        return $url;
    }
}
```

## Nhúng vào Layout chính của bạn.
```html
<link rel="stylesheet" href="{{ asset('css/wp-blocks.css') }}">

<article class="wp-content-area">
    <h1>{{ $post->post_title }}</h1>
    <div class="content">
        {!! $post->post_content !!}
    </div>
</article>
```

## Ví dụ thực thi: Lấy thông tin bài viết (WP) và kết hợp dữ liệu nội bộ (Laravel)
```php
namespace App\Http\Controllers;

use App\Models\Post; // Model Corcel trỏ tới Connection 'wordpress'
use Illuminate\Support\Facades\DB; // DB mặc định trỏ tới Connection 'mysql'

class PostController extends Controller
{
    public function show($slug)
    {
        // 1. Lấy dữ liệu bài viết từ Connection WordPress (Dành cho SEO/Public)
        // Corcel tự động hiểu và dùng prefix 'wp_' qua Connection 'wordpress'
        $post = Post::slug($slug)->firstOrFail();

        // 2. Lấy dữ liệu lượt xem từ Connection Laravel (Dữ liệu nội bộ)
        // Kết nối này dùng bảng 'analytics' không có prefix
        $stats = DB::connection('mysql')
                    ->table('post_analytics')
                    ->where('post_id', $post->ID)
                    ->first();

        // 3. Tăng lượt xem (Xử lý nội bộ Laravel)
        DB::table('post_analytics')->updateOrInsert(
            ['post_id' => $post->ID],
            ['views' => DB::raw('views + 1')]
        );

        return view('posts.show', compact('post', 'stats'));
    }
}
```

## Tối ưu hóa SEO và Hiệu suất với Caching

```php
use Illuminate\Support\Facades\Cache;

$post = Cache::remember("post_{$slug}", 3600, function () use ($slug) {
    return Post::slug($slug)->with('meta', 'taxonomies')->first();
});
```


