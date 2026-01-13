# woocommerce
> https://github.com/corcel/woocommerce/tree/master

```model
<?php

namespace App\Models;

// use Illuminate\Database\Eloquent\Model;
use Corcel\WooCommerce\Model\Product as CorcelWCProduct;

class WCProduct extends CorcelWCProduct
{
    protected $connection = 'wordpress';
}

```