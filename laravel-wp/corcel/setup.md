# https://github.com/corcel/corcel

> cd-laravel

> composer require jgrossi/corcel

> Corcel\Laravel\CorcelServiceProvider::class,

composer require jgrossi/corcel:^9.0 -W

> composer show jgrossi/corcel

> \Corcel\Model\Post::on('corcel')->first();

'corcel' => [
            'driver' => 'mysql',
            'url' => env('DB_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'laravel'),
            'username' => env('DB_USERNAME', 'root'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => env('DB_CHARSET', 'utf8mb4'),
            'collation' => env('DB_COLLATION', 'utf8mb4_unicode_ci'),
            'prefix' => 'wp_',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                (PHP_VERSION_ID >= 80500 ? Mysql::ATTR_SSL_CA : PDO::MYSQL_ATTR_SSL_CA) => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],


## Khi bạn ghi vào SQLite, hãy dùng updateOrCreate

'sqlite' => [
            'driver' => 'sqlite',
            'url' => env('DB_URL'),
            'database' => env('DB_DATABASE', database_path('database.sqlite')),
            'prefix' => '',
            'foreign_key_constraints' => env('DB_FOREIGN_KEYS', true),

            // --- ĐÂY LÀ PHẦN QUAN TRỌNG ---
            'busy_timeout' => 5000,      // Đợi 5 giây nếu DB đang bị khóa trước khi báo lỗi
            'journal_mode' => 'WAL',     // Bật chế độ Write-Ahead Logging (Vừa đọc vừa ghi thoải mái)
            'synchronous'  => 'NORMAL',  // Tăng tốc độ ghi (giảm bớt việc bắt ổ cứng xác nhận liên tục)
            'transaction_mode' => 'IMMEDIATE', // Bắt đầu Transaction ngay lập tức để tránh xung đột
        ],


        Giải pháp: Đảm bảo bạn đã bật WAL Mode (như mình đã nói ở trên). Trong chế độ WAL, SQLite sử dụng một file -shm (Shared Memory). File này cho phép hàng ngàn tiến trình PHP đọc dữ liệu trực tiếp từ bộ nhớ đệm mà không cần xếp hàng đợi nhau.