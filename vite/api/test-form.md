## laravel - test
Route::post('/test-form', function (Request $request) {
    // Trả về toàn bộ dữ liệu nhận được để kiểm tra
    return response()->json([
        'method' => 'POST',
        'all_data' => $request->all(), // Cách của Laravel
        'raw_post' => $_POST          // Cách thuần PHP bạn muốn xem
    ]);
});
