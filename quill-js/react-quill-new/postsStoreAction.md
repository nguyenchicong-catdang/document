# actions/posts/postsStoreAction.jsx

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
