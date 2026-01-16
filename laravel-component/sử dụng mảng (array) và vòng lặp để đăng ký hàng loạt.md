# sử dụng mảng (array) và vòng lặp để đăng ký hàng loạt
##  Cách dùng Mảng để đăng ký (Khuyên dùng)
```php
public function boot(): void
{
    // 1. Đăng ký Namespace chung cho các Class (Header.php, Sidebar.php...)
    Blade::componentNamespace('MyPackage\\Components', 'layout');

    // 2. Đăng ký hàng loạt Anonymous Components bằng mảng
    $components = [
        'header'  => __DIR__.'/../src/Components/Header',
        'sidebar' => __DIR__.'/../src/Components/Sidebar',
        'content' => __DIR__.'/../src/Components/Content',
        'footer'  => __DIR__.'/../src/Components/Footer',
    ];

    foreach ($components as $prefix => $path) {
        Blade::anonymousComponentPath($path, $prefix);
    }
}
```
## Kết quả gọi trong View:
```md
<x-layout::header /> (Chạy qua Class để lấy dữ liệu Model)

<x-header::logo /> (Vào thẳng thư mục Header/logo.blade.php)

<x-sidebar::item /> (Vào thẳng thư mục Sidebar/item.blade.php)
```
## Cách tự động quét thư mục (Dynamic)
```php
public function boot(): void
{
    Blade::componentNamespace('MyPackage\\Components', 'layout');

    $basePath = __DIR__.'/../src/Components';
    
    // Lấy tất cả các thư mục con bên trong src/Components
    $directories = array_filter(glob($basePath . '/*'), 'is_dir');

    foreach ($directories as $dir) {
        $folderName = strtolower(basename($dir));
        
        // Bỏ qua thư mục 'views' nếu bạn để view của class ở đó
        if ($folderName === 'views') continue;

        Blade::anonymousComponentPath($dir, $folderName);
    }
}
```
## Tổ chức dữ liệu và Truyền dữ liệu (Data Flow)
```blade.php
<aside>
    @foreach($menuItems as $item)
        {{-- Gọi anonymous component 'item' nằm trong thư mục Sidebar/ --}}
        <x-sidebar::item :label="$item->name" :icon="$item->icon" />
    @endforeach
</aside>
```