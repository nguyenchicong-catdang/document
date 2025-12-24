# artisan-custum
```bash
#!/usr/bin/env php
<?php
// example-app/artisan
use Illuminate\Foundation\Application;
use Symfony\Component\Console\Input\ArgvInput;

define('LARAVEL_START', microtime(true));

// Register the Composer autoloader...
require __DIR__.'/vendor/autoload.php';

// Bootstrap Laravel and handle the command...
/** @var Application $app */
//$app = require_once __DIR__.'/bootstrap/app.php';
// xu ly bootstrap -> trong src
$app = require_once __DIR__ . '/src/bootstrap/app.php';
$status = $app->handleCommand(new ArgvInput);

exit($status);

```