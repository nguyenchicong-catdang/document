# them route
```bash
then: function () {
            Route::middleware('api') // Áp dụng các middleware của nhóm API (throttle, auth, v.v.)
                ->prefix('learn-api')      // Thêm tiền tố /api
                ->group(__DIR__ . '/../routes/route-lean-api.php');
        },
```
