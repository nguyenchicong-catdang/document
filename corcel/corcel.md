# corcel
https://github.com/corcel/corcel

> composer require jgrossi/corcel

# include CorcelServiceProvider
> Corcel\Laravel\CorcelServiceProvider::class,

# Publishing the configuration file
> php artisan vendor:publish --provider="Corcel\Laravel\CorcelServiceProvider"

> composer require corcel/woocommerce

```model
<?php

namespace App\Models;

// use Illuminate\Database\Eloquent\Model;
use Corcel\Model\Post as CorcelPost;

class WPPost extends CorcelPost
{
    protected $connection = 'wordpress';

    public function customMethod()
    {
        //
    }
}

```