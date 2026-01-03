# laravel
## DatabaseSeeder
```php
        $this->call([
            LearnApiSeeder::class,
            PostsSeeder::class
        ]);

```
>[quill-js/laravel/DatabaseSeeder.md](quill-js.md)
## migrations
> $table->json('content')->nullable(); // Sử dụng json để tối ưu  
>[quill-js/laravel/factories.md](laravel/migrations.md)
## factories
```php
$quillDelta = [
    'ops' => [
        ['insert' => fake()->sentence(10, true) ."\n"],
        ['insert' => "Văn bản in đậm mẫu", 'attributes' => ['bold' => true]],
    ]
];
return [
    'title' => fake()->realText(50),
    // Laravel sẽ tự encode array này thành JSON nhờ Cast ở Model
    'content' => $quillDelta,
];
```
>[quill-js/laravel/factories.md](laravel/migrations.md)
## Model
```php
    protected function casts(): array
    {
        return [
            'content' => 'json',
        ];
    }
```
>[quill-js/quill-js.md](quill-js.md)
## Controller
```php
$validated = $request->validated();
// store
Posts::create($validated);
return response()->json('', 201);
```
>[quill-js/quill-js.md](quill-js.md)
# react-quill-new
## Component ReactQuill
```jsx
import { useState } from "react"
import ReactQuill from "react-quill-new"
import 'react-quill-new/dist/quill.snow.css'

export default function QuillEditor() {
   const [editorHtml, setEditorHtml] = useState(''); // Dùng để hiển thị
   const [jsonValue, setJsonValue] = useState(''); // Dùng để gửi form
   const onChangeHandler = (content, delta, source, editor) => {
      setEditorHtml(content); // Cập nhật HTML cho editor
      const rowJson = JSON.stringify(editor.getContents()); // Lấy định dạng Delta (JSON)
      setJsonValue(rowJson)
   }
   return (
      <div style={{margin: '10px'}}>
         {/* Input này sẽ gửi dữ liệu JSON lên Server qua Form */}
         <input type="hidden" name="content" value={jsonValue} />
         <ReactQuill
            theme="snow"
            value={editorHtml}
            onChange={onChangeHandler}
         />
      </div>
   )
}
```
>[quill-js/react-quill-new/QuillEditor.md](react-quill-new/QuillEditor.md)
# page
## PostStore
```jsx
import { Form, useActionData } from "react-router"; // [1] Import Form từ react-router
import QuillEditor from "../QuillEditor";
export default function PostsStore() {
   // tra ve null neu dang nhap lan dau -> chua co action
   const actionData = useActionData() ?? {};
   const arrMess = Object.values(actionData)

   //const arrMess = actionData ? Object.values(actionData) : [];
   //console.log(arrMess)
   return (
      <div>
         <h1>Tao bai viet moi</h1>
         <Form method="post">
            Title: <input type="text" name="title" /> <br />
            {/* Content: <textarea name="content" rows="10"></textarea> <br /> */}
            <QuillEditor /> <br />
            <button type="submit">Tao bai viet moi</button>
         </Form>
         <div style={{ color: 'red' }}>
            {arrMess.map((data, index) => (
               <p key={index}>{data}</p>
            ))}
         </div>
      </div>
   )
}

```
>[quill-js/react-quill-new/PostsStore.md](react-quill-new/PostsStore.md)
## PostIndex
```jsx
import { Link, useLoaderData } from "react-router"
import ReactQuill from "react-quill-new";
import 'react-quill-new/dist/quill.bubble.css'; // Dùng theme bubble cho gọn
export default function PostsIndex() {
   const posts = useLoaderData();
   console.log(posts)
  return (
      <div>
          <h1>Danh sach bai viet</h1>
          <ul>
              {posts.map(post => (
                  <li key={post.id}>
                      {/* <h3><a href="#">{post.title}</a></h3> */}
                      <Link to={`/posts/${post.id}`}>{post.title}</Link>
                      {/* Nút Chỉnh sửa */}
                      <Link to={`/posts/${post.id}/edit`}>
                          <button style={{ backgroundColor: 'orange' }}>Edit</button>
                    </Link>
                    {/* Hiển thị nội dung Quill */}
                    {/* <p>{ post.content}</p> */}
                    <div>
                       {post.content ? (
                          <ReactQuill
                             // Nếu Laravel chưa cast, dùng JSON.parse(post.content)
                             // Nếu Laravel đã cast, dùng luôn post.content
                             value={typeof post.content === 'string' ? JSON.parse(post.content) : post.content}
                             readOnly={true}
                             theme="bubble"
                             modules={{toolbar:false}}
                          />
                       ) : (
                             <p>Không có nội dung</p>
                       )}
                    </div>
                  </li>
              ))}
          </ul>
    </div>
  )
}

```
>[quill-js/react-quill-new/PostsIndex.md](react-quill-new/PostsIndex.md)
# loader
## postsLoader
```jsx
export async function postsLoader() {
    const token = localStorage.getItem('laravel_token');

    const response = await fetch('/api/posts', {
        method: 'get',
        headers: {
            'Accept': 'application/json',
            'Authorization': 'Bearer ' + token
        }
    });

    if (response.ok) {
        return await response.json()
    }
    
    throw new Response(response.statusText, {status: response.status})
}
```
>[react-quill-new/postsLoader.md](react-quill-new/postsLoader.md)
# action
## postsStoreAction
```jsx
import { redirect } from "react-router";
export async function postsStoreAction({ request }) {
	const formData = await request.formData();
	const token = localStorage.getItem("laravel_token");
	const response = await fetch("/api/posts", {
		method: "post",
		headers: {
			Accept: "application/json",
			Authorization: "Bearer " + token,
		},
		body: formData,
	});

	if (response.ok) {
		return redirect("/posts");
	} 

    throw new Response(response.statusText, {status:response.status})
}
```
>[react-quill-new/postsStoreAction.md](react-quill-new/postsStoreAction.md)
