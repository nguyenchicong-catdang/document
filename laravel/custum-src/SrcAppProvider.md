# Cấu hình -> quan trọng: SrcAppProvider
```bash
<?php

namespace SrcApp\Providers;

use Illuminate\Support\ServiceProvider;

class SrcAppProvider extends ServiceProvider
{
    /**
     * Register services.
     */
    public function register(): void
    {
        $this->app->bind(
            \SrcApp\Contracts\UserRepositoryInterface::class,
            \SrcApp\Repositories\UserRepository::class
        );
    }

    /**
     * Bootstrap services.
     */
    public function boot(): void
    {
        // 1. Tự động nạp Migrations từ thư mục src
        $this->loadMigrationsFrom(__DIR__ . '/../../database/migrations');
        // Ép Laravel dùng LoginModel của bạn cho mọi hoạt động Auth
        config(['auth.providers.users.model' => \SrcApp\Models\LoginModel::class]);
        // view
        $this->loadViewsFrom(base_path('src/app/Views'), 'src');

        // Cho phép copy view ra ngoài bằng lệnh artisan
        // run php artisan vendor:publish --tag=custom-views

        if ($this->app->runningInConsole()) {
            // use -->> php artisan vendor:publish --tag=src-views
            $this->publishes([
                base_path('src/app/Views') => resource_path('views/vendor/custom'),
            ], 'src-views');
        }
        logger('Provider loaded');
    }
}

```

## bootstrap/app.php
```bash
<?php

use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Foundation\Configuration\Middleware;
// thay return -> $app; dirname(__DIR__,2) -> 2 root composer.json
$app = Application::configure(basePath: dirname(__DIR__,2))
    ->withRouting(
        web: __DIR__ . '/../routes/web.php',
        commands: __DIR__ . '/../routes/console.php',
    //commands: dirname(__DIR__, 2) . '/routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware): void {
        //
    })
    ->withExceptions(function (Exceptions $exceptions): void {
        //
    })->create();

// Thiết lập lại đường dẫn App Path cho instance này
$app->useAppPath(base_path('src/app'));
//$app->register(\SrcApp\Providers\SrcAppProvider::class);
//$app->withRegisteredProviders(require __DIR__ . '/providers.php');
// 2. Nạp danh sách providers từ file providers.php một cách tự động
// ĐĂNG KÝ THỦ CÔNG DANH SÁCH TỪ FILE PROVIDERS.PHP ĐỂ ĐẢM BẢO HIỆU LỰC
// $providers = require __DIR__ . '/providers.php';
// foreach ($providers as $provider) {
//     if (class_exists($provider)) {
//         $app->register($provider);
//     }
// }

/**
 * Dùng Container để đăng ký Service Providers từ file cấu hình
 */
$providers = require __DIR__ . '/providers.php';

foreach ($providers as $provider) {
    // Container sẽ tự động khởi tạo (instantiate) và đăng ký provider này
    $app->register($provider);
}
return $app;

```

## tự động nap src/bootstrap/providers.php
```bash
<?php
// src/bootstrap/providers.php

$providerFiles = glob(__DIR__ . '/../app/Providers/*Provider.php');

return array_map(function ($file) {
    $class = 'SrcApp\\Providers\\' . basename($file, '.php');
    return $class;
}, $providerFiles);

```
