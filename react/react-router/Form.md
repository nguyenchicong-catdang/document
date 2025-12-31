# From
https://react.dev/reference/react-dom/components/input#providing-an-initial-value-for-an-input

```bash
import { Form, useLoaderData, useActionData, useNavigation } from "react-router-dom";

export default function PostsEdit() {
    const post = useLoaderData();
    const actionData = useActionData(); // Lấy dữ liệu trả về từ hàm action (nếu có lỗi)
    const navigation = useNavigation(); // Dùng để kiểm tra trạng thái đang gửi dữ liệu
    
    const isSubmitting = navigation.state === "submitting";

    return (
        <div>
            <h2>Chỉnh sửa bài viết</h2>
            
            {/* Nếu action trả về lỗi, hiển thị ở đây */}
            {actionData?.error && <p style={{color: 'red'}}>{actionData.error}</p>}

            <Form method="post"> 
                {/* Router 7 sẽ bắt sự kiện submit này */}
                <div>
                    <label>Tiêu đề:</label>
                    <input 
                        type="text" 
                        name="title" 
                        defaultValue={post.title} 
                    />
                </div>

                <div>
                    <label>Nội dung:</label>
                    <textarea 
                        name="content" 
                        defaultValue={post.content} 
                    />
                </div>

                <button type="submit" disabled={isSubmitting}>
                    {isSubmitting ? "Đang lưu..." : "Lưu thay đổi"}
                </button>
            </Form>
        </div>
    );
}
```
# action
```bash
// postsAction.js hoặc viết ngay trong file component
import { redirect } from "react-router-dom";

export async function postsUpdateAction({ request, params }) {
    const id = params.id;
    const formData = await request.formData();
    
    // Chuyển formData thành object đơn giản
    const updates = Object.fromEntries(formData);

    const response = await fetch(`/api/posts/${id}`, {
        method: 'PUT', // Hoặc PATCH tùy API Laravel của bạn
        headers: {
            'Content-Type': 'application/json',
            'Accept': 'application/json',
            // 'Authorization': 'Bearer ' + localStorage.getItem('token')
        },
        body: JSON.stringify(updates),
    });

    if (response.ok) {
        // Nếu thành công, điều hướng người dùng về trang danh sách
        return redirect("/posts");
    }

    // Nếu có lỗi (ví dụ lỗi 422 từ Laravel), trả về lỗi để hiển thị
    return { error: "Cập nhật thất bại, vui lòng kiểm tra lại dữ liệu" };
}
```
## routes.js
```bash
// App.jsx hoặc routes.js
{
    path: "posts/:id/edit",
    element: <PostsEdit />,
    loader: postsEditLoader,   // Lấy dữ liệu cũ
    action: postsUpdateAction, // Xử lý submit dữ liệu mới
    errorElement: <ErrorPage />
}
```
## useActionData
```bash
export async function postsUpdateAction({ request, params }) {
    const formData = await request.formData();
    const data = Object.fromEntries(formData);

    const response = await fetch(`/api/posts/${params.id}`, {
        method: 'PUT',
        body: JSON.stringify(data),
        headers: { 'Content-Type': 'application/json' }
    });

    // Nếu Laravel trả về lỗi validation (status 422)
    if (response.status === 422) {
        const errorData = await response.json();
        return errorData; // Trả về object lỗi (ví dụ: { message: "...", errors: { title: ["Quá ngắn"] } })
    }

    if (response.ok) return redirect("/posts");
}
```
## Tại Component PostsEdit (hiển thị UI):
```bash
import { Form, useActionData } from "react-router-dom";

export default function PostsEdit() {
    const actionData = useActionData(); // Đây là nơi nhận dữ liệu từ return của action

    return (
        <Form method="post">
            <input name="title" defaultValue="..." />
            
            {/* Nếu có lỗi từ Laravel cho trường title, hiển thị nó ra */}
            {actionData?.errors?.title && (
                <p style={{ color: "red" }}>{actionData.errors.title[0]}</p>
            )}

            <button type="submit">Cập nhật</button>
        </Form>
    );
}
```