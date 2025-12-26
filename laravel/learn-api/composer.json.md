## composer.json
```bash
"autoload": {
    "psr-4": {
        "App\\": "src/app/",
        "Database\\Factories\\": "src/database/factories/",
        "Database\\Seeders\\": "src/database/seeders/"
    }
}

```

## run omposer dump-autoload
```bash
omposer dump-autoload
```