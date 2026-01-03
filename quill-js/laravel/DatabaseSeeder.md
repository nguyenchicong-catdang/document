# seeders/DatabaseSeeder.php
```php
    public function run(): void
    {
        $this->call([
            LearnApiSeeder::class,
            PostsSeeder::class
        ]);
        User::factory(5)->create();

        User::factory()->create([
            'name' => 'Admin User',
            'username' => 'admin',
            'email' => 'admin@example.com',
            'password' => Hash::make('YOUR_PASS'),
        ]);
    }

```
