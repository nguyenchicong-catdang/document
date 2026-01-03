# loaders/posts/postsLoader.jsx
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
