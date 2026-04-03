> curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb -o cloudflared.deb
sudo dpkg -i cloudflared.deb

> cloudflared tunnel --url http://127.0.0.1:8000

Chạy Laravel ngầm: php artisan serve &

Chạy Cloudflare ngầm: nohup cloudflared tunnel --url http://127.0.0.1:8000 > cloudflare.log 2>&1 &

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\URL; // Thêm dòng này

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Kiểm tra nếu không phải là localhost thì ép sang https
        if (env('APP_ENV') !== 'local') {
            URL::forceScheme('https');
        }
    }
}
```

# Cách 2: "Mẹo" nhanh trong file Blade

<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">

