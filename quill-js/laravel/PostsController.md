# Http/Controllers/PostsController.php
```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\StorePostsRequest;
use App\Http\Requests\UpdatePostsRequest;
use App\Models\Posts;
use Illuminate\Http\JsonResponse;

class PostsController extends Controller
{
    /**
     * Display a listing of the resource.
     */
    public function index() :JsonResponse
    {
        //
        //$posts = Posts::all();
        $posts = Posts::latest()->get();
        return response()->json($posts,200);

    }

    /**
     * Show the form for creating a new resource.
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     */
    public function store(StorePostsRequest $request) :JsonResponse
    {
        // $post = ['title'=>'test1', 'content'=> 'content1']
        $validated = $request->validated();
        Posts::create($validated);
        return response()->json('', 201);
    }

    /**
     * Display the specified resource.
     */
    public function show(Posts $post): JsonResponse
    {
        //
        return response()->json($post, 200);
    }
    /**
     * Show the form for editing the specified resource.
     */
    public function edit(Posts $post): JsonResponse
    {
        //
        return response()->json($post, 200);
    }

    /**
     * Update the specified resource in storage.
     */
    public function update(UpdatePostsRequest $request, Posts $post)
    {
        //
        $post->update($request->validated());
        return response()->json($post, 200);
    }

    /**
     * Remove the specified resource from storage.
     */
    public function destroy(Posts $post)
    {
        $post->delete();
        return response()->json(null, 204);
    }
}

```
