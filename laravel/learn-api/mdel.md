# run
php artisan make:model LearnApi -m

php artisan make:migration create_users_table --path=src/database/migrations

## php artisan migrate
php artisan migrate

# model
```bash
<?php

namespace SrcApp\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Laravel\Sanctum\HasApiTokens;
//use Illuminate\Database\Eloquent\Model;

class LearnApi extends Authenticatable
{
    //
    use HasApiTokens;
    protected $table = 'learn_apis'; // Chỉ định rõ tên bảng
    //https://laravel.com/docs/12.x/eloquent#allowing-mass-assignment
    //protected $guarded = [];
    protected $fillable = [
        'username',
        'password',
        'api_key' // Nếu bạn có cột này
    ];
}

```
## database/migrations/2025_12_25_075059_create_learn_apis_table.php

```bash
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('learn_apis', function (Blueprint $table) {
            $table->id();

            // Trường đăng nhập (Có thể dùng username hoặc email)
            $table->string('username')->unique();

            // Password phải đủ độ dài để lưu chuỗi đã Bcrypt (thường là 255)
            $table->string('password');

            // api_key nếu bạn muốn dùng như một mã định danh tĩnh bổ sung
            $table->string('api_key')->nullable()->unique();

            // (Tùy chọn) Để dùng tính năng "Remember me" của Laravel
            $table->rememberToken();

            // Tạo 2 cột created_at và updated_at
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('learn_apis');
    }
};

```

