# throw new Response("Lỗi kết nối mạng", { status: 503 });
--> chỉ băt 503 nếu submit -> mat mang

```bash
// actions/posts/postsUpdateAction.jsx

// https://api.reactrouter.com/v7/interfaces/react_router.ActionFunctionArgs.html
export async function postsUpdateAction({ request, params }) {
    try {
        let id = params.id
        let formData = await request.formData()
        // let title = formData.get('title')
        // console.log(id)

        const response = await fetch(`/api/posts/${id}`);
        //console.log(await response.text())
        if (response.ok) {
            return await response.json()
        } else {
            throw new Response(response.statusText,{status:response.status})
        }
    } catch (err) {
        if (err instanceof Response) {
            throw err
        }
        // Trường hợp lỗi mạng thực sự (mất mạng, DNS lỗi)
        throw new Response("Lỗi kết nối mạng", { status: 503 });
    }
}

```