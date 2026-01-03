# components/posts/PostsStore.jsx
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
