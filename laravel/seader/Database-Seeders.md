# php artisan make:seeder LearnApiSeeder
php artisan make:seeder LearnApiSeeder
# code
```bash
<?php

namespace Database\Seeders;

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;

class LearnApiSeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        //
        \SrcApp\Models\LearnApi::create([
            'username' => 'admin',
            'password' => bcrypt('123456'), // Luôn luôn mã hóa password
            'api_key'  => 'key_mac_dinh_cua_ban', // Thêm dòng này để tránh lỗi NOT NULL
        ]);
    }
}
// php artisan migrate:fresh --seed
// php artisan make:seeder LearnApiSeeder
// php artisan db:seed --class=LearnApiSeeder

```
## php artisan migrate:fresh --seed
### /home/cong/openLiteSpeed/example-app/database/seeders/DatabaseSeeder.php
```bash
<?php

namespace Database\Seeders;

use App\Models\User;
use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    use WithoutModelEvents;

    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        $this->call([
            LearnApiSeeder::class,
        ]);
        // User::factory(10)->create();

        // User::factory()->create([
        //     'name' => 'Test User',
        //     'email' => 'test@example.com',
        // ]);
    }
}

```

## run

// php artisan migrate:fresh --seed
// php artisan make:seeder LearnApiSeeder
// php artisan db:seed --class=LearnApiSeeder