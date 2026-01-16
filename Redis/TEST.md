# TEST

> Illuminate\Support\Facades\Redis::connection('cache')

> Illuminate\Support\Facades\Redis::connection('cache')->ttl('laravel-cache-wp_sidebar_categories');

> Illuminate\Support\Facades\Redis::connection('cache')->client()->keys('*');

> cache()->get('wp_sidebar_categories');

## tinker

> config('cache.prefix');

> config('database.redis.options.prefix');

## wsl

> redis-cli -n 1

> KEYS *

> TTL laravel-database-laravel-cache-wp_sidebar_categories