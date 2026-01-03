# migrations/2025_12_29_095301_create_posts_table.php

```php
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            //$table->longText('content');
            // Sử dụng kiểu json để tối ưu cho Delta Quill
            $table->json('content')->nullable();
            $table->timestamps();
        });
    }

```
