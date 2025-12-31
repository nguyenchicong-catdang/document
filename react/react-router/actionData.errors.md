# actionData.errors
```bash
// https://react.dev/reference/react-dom/components/input#providing-an-initial-value-for-an-input
import { Form, useActionData, useLoaderData } from "react-router"

export default function PostsEdit() {
    const post = useLoaderData();
    const actionData = useActionData();

    console.log(actionData.errors)
  return (
      <div>
          <Form method="post">
              Title: <input type="text" name="title" defaultValue={post.title } /><br />
              Content: <textarea name="content" rows="10" defaultValue={post.content}></textarea><br />
              <button type="submit">Update</button><br />
              <button type="button">Cance</button>
          </Form>
          <div className="err-mess">
              {
                  actionData?.errors && (
                      Object.entries(actionData.errors).map(([key, err]) => (
                          <p style={{color:'red'}} key={key}>{err}</p>
                        )
                      )
                  )
                }
          </div>
    </div>
  )
}

```