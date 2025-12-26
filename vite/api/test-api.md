## test
```bash
// vite/test/api-test-method.js

const root = document.getElementById('root');

function render() {

    const html = /* html */ `
    <button onclick="getMethod()">Test get</button>
    <button onclick="postMethod()">Test post</button>
    `
    root.innerHTML = html
}
window.getMethod = () => {
    console.log(1)
}

window.postMethod = () => {
    console.log(2)
}


document.addEventListener('DOMContentLoaded',
    render,
);

```