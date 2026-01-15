## laravel component
> php artisan make:component WpMenu

# Viết Logic trong Class Component
```php
namespace App\View\Components;

use Illuminate\View\Component;
use Wp\Posts\Post;

class WpMenu extends Component
{
    public $items; // Biến này sẽ tự động có mặt ở file Blade

    public function __construct($slug)
    {
        // Component tự gọi Model, giống như một "mini-controller"
        $nav = Post::type('wp_navigation')->slug($slug)->first();
        
        $this->items = $this->parseBlocks($nav ? $nav->post_content : '');
    }

    private function parseBlocks($content)
    {
        // Logic Regex bóc tách chuỗi block WordPress mà bạn đã làm
        preg_match_all('/"label":"([^"]+)".*?"url":"([^"]+)"/', $content, $matches);
        
        return collect($matches[1])->map(function ($label, $index) use ($matches) {
            return (object) ['title' => $label, 'url' => $matches[2][$index]];
        });
    }

    public function render()
    {
        return view('components.wp-menu');
    }
}
```

## Viết Giao diện trong Blade Component

```php
<nav class="wp-menu-container">
    <ul>
        @foreach($items as $item)
            <li><a href="{{ $item->url }}">{{ $item->title }}</a></li>
        @endforeach
    </ul>
</nav>
```
## Cách sử dụng trong trang bất kỳ
```php
<x-wp-menu slug="navigation-copy" />
```

## Cách đăng ký Namespace tùy chỉnh (Custom Load)

```php
use Illuminate\Support\Facades\Blade;

public function boot()
{
    // Đăng ký namespace "wp" cho thư mục tùy chỉnh
    // Tham số 1: Namespace của Class PHP
    // Tham số 2: Tiền tố (prefix) khi gọi trong Blade
    Blade::componentNamespace('App\\WpComponents\\View\\Components', 'wp');
}
```

## Cấu trúc thư mục mới của bạn
```md
ây giờ bạn có thể đặt code ở bất cứ đâu bạn muốn:

Class: app/WpComponents/View/Components/Menu.php

View: resources/views/components/wp/menu.blade.php (hoặc bạn có thể dùng View nằm trong Class).
```
## Cách gọi trong Blade
```php
<x-wp::menu slug="navigation-copy" />
```

## ESI Tag: Bạn bao bọc các Component này bằng thẻ <esi:include src="/sidebar-fragment" />