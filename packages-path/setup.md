# trong packages src

```composer.json
{
  "name": "vendor-path/packages-src",
  "description": "Một package siêu cấp SRC",
  "autoload": {
    "psr-4": {
      "Vendorpath\\Src\\": "src/",
      "Vendorpath\\Wp\\": "wp/"
    }
  }
}

```

# trong dự án

```composer.json
{
    "repositories": [
        {
            "type": "path",
            "url": "../packages-src",
            "options": {
                "symlink": true
            }
        }
    ],
    "require": {
        "vendor-path/packages-src": "*"
    },
    "minimum-stability": "dev",
    "prefer-stable": true
}
```

# chay lệnh tron laravel app

> composer update vendor-path/packages-src --prefer-source

# packages-app.code-workspace

```packages-app.code-workspace
{
      "name": "PROJECT SRC",
      "path": "./laravel-app/vendor/vendor-path/packages-src", // Đây là /home/cong/git/packages-app
    },
```
