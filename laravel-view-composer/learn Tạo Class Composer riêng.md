# default Tạo Service Provider để quản lý dữ liệu
```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\View;
use Corcel\Model\Taxonomy;
use Wp\Posts\Post;

class ViewServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Chỉ đổ dữ liệu vào file partials.sidebar khi nó được load
        View::composer('partials.sidebar', function ($view) {
            $categories = Taxonomy::where('taxonomy', 'category')
                ->where('count', '>', 0)
                ->with('term')
                ->get();
            $view->with('sidebarCategories', $categories);
        });

        // Chỉ đổ dữ liệu vào header khi nó được load
        View::composer('partials.header', function ($view) {
            $nav = Post::type('wp_navigation')->slug('navigation-copy')->first();
            // Xử lý chuỗi lấy label/url như đã thảo luận ở trên
            $view->with('headerMenu', $this->parseNav($nav));
        });
    }
}
```
## Cách sử dụng trong Blade tổng (Layout)
```php
<html>
    <body>
        @include('partials.header') {{-- Tự động có biến $headerMenu --}}
        
        <main>
            @yield('content') {{-- Dữ liệu từ Controller chính --}}
        </main>

        @include('partials.sidebar') {{-- Tự động có biến $sidebarCategories --}}
    </body>
</html>
```


# Tạo Class Composer riêng
```php
namespace App\Http\View\Composers;

use Illuminate\View\View;
use Corcel\Model\Taxonomy;

class SidebarComposer
{
    /**
     * Bind data to the view.
     */
    public function compose(View $view)
    {
        // Toàn bộ logic lấy dữ liệu nằm ở đây
        $categories = Taxonomy::where('taxonomy', 'category')
            ->where('count', '>', 0)
            ->with('term')
            ->get();

        $view->with('sidebarCategories', $categories);
    }
}
```

# Đăng ký trong ViewServiceProvider

```php
public function boot()
{
    // Thay vì viết logic, bạn chỉ cần trỏ đến Class đã tạo
    View::composer('partials.sidebar', \App\Http\View\Composers\SidebarComposer::class);
    
    // Tương tự cho Header
    View::composer('partials.header', \App\Http\View\Composers\HeaderComposer::class);
}
```

## Nâng cấp hơn: Sử dụng Service Class (Nếu logic phức tạp)

```php
namespace App\Services;

use Wp\Posts\Post;

class WordpressService
{
    public function getNavigation($slug)
    {
        $nav = Post::type('wp_navigation')->slug($slug)->first();
        if (!$nav) return [];

        // Logic xử lý chuỗi JSON/Block bạn muốn
        preg_match_all('/"label":"([^"]+)".*?"url":"([^"]+)"/', $nav->post_content, $matches);
        
        return collect($matches[1])->map(function($label, $index) use ($matches) {
            return [
                'title' => $label,
                'url' => $matches[2][$index]
            ];
        });
    }
}
```

## Sử dụng Service trong Composer
```php
public function compose(View $view)
{
    $wpService = new \App\Services\WordpressService();
    $view->with('headerMenu', $wpService->getNavigation('navigation-copy'));
}
```

## Sử dụng tham số thứ hai của @include (Nhanh nhất)
```php
{{-- Trong file content.blade.php --}}
@php
    $customTitle = "Tiêu đề riêng cho trang này";
@endphp

@include('partials.header-theo-noidung', ['title' => $customTitle, 'pageId' => $post->ID])
```

## Sử dụng @stack và @push (Dành cho CSS/JS hoặc HTML bổ sung)
```php
<head>
    @stack('header_tags')
</head>
```
## Tại file Content (trang con):
```php
@push('header_tags')
    <meta name="description" content="Nội dung mô tả riêng của bài viết">
    <link rel="canonical" href="{{ url()->current() }}">
@endpush
```

## Sử dụng @section và @yield (Dành cho nội dung lớn)
```php
<div class="header-banner">
    @yield('header_content', 'Nội dung mặc định nếu trang con không truyền gì')
</div>
```

## Tại file Content:
```php
@section('header_content')
    <h1>Đây là tiêu đề lấy từ bài viết: {{ $post->post_title }}</h1>
@endsection
```
## Chia sẻ dữ liệu toàn cục qua Controller (Global Data)
```php
public function show($slug) {
    $post = Post::slug($slug)->first();
    
    // Chia sẻ biến $currentPost cho tất cả các file Blade
    view()->share('currentPost', $post); 
    
    return view('page');
}
```