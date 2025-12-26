# src/bootstrap/app.php
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

return $app;
```