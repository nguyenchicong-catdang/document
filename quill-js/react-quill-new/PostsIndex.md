# components/posts/PostsIndex.jsx
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
