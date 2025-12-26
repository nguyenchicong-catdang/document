# Cấu hình Middleware trong bootstrap/app.php (Laravel 11)
->withMiddleware(function (Middleware $middleware) {
    $middleware->statefulApi(); // Rất quan trọng để API chấp nhận Cookie
})

## phân biệt giữa Stateless (API Token) và Stateful (Session/Cookie).

## Trong config/sanctum.php
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', 'localhost,127.0.0.1')),
## Trong Middleware (Laravel 11)
// bootstrap/app.php
->withMiddleware(function (Middleware $middleware) {
    $middleware->statefulApi(); 
})