# [text](req-json1.md)
```bash
async function reqServerJson(elmForm) {
    const username = elmForm.querySelector('input[name=username]').value;
    const password = elmForm.querySelector('input[name=password]').value;

    const data = {
        username: username,
        password: password
    }
    const response = await fetch('/api/test-form', {
        method: 'POST', // Nên viết hoa cho đúng chuẩn
        headers: {
            'Content-Type': 'application/json', // Bắt buộc phải có để Laravel hiểu đây là JSON
            'Accept': 'application/json'        // Để Laravel trả về lỗi dạng JSON nếu có
        },
        body: JSON.stringify(data) // Chuyển object thành chuỗi JSON
    });
    //console.log(data)
    const result = await response.json();
    console.log(result)
}
```

## ver 2
```bash
async function reqServerJson(elmForm) {
    // Tự động lấy tất cả data từ Form
    const formData = new FormData(elmForm);
    const data = Object.fromEntries(formData.entries());

    const response = await fetch('/api/test-form', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Accept': 'application/json',
            // 'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').content // Nếu dùng Web middleware
        },
        body: JSON.stringify(data)
    });
    // ...
}
```