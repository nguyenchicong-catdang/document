# js-token-laravel
```bash
// login/test.js
//console.log(1)
function handleSubmit() {
    const elmForm = document.getElementById('loginForm')
    //console.log(elmForm)
    elmForm.addEventListener('submit', (e) => {
        e.preventDefault();
        reqServer(elmForm);
    })
}

async function reqServer(elmForm) {
    const formData = new FormData(elmForm)
    let elmErrMess = elmForm.querySelector('.err-mess');
    const data = Object.fromEntries(formData.entries());
    try {
        const response = await fetch('/api/login', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json' // Quan trọng: Để Laravel biết trả về JSON thay vì 302
            },
            body: JSON.stringify(data) // Gửi chuỗi JSON
            //body: formData
        });
        const result = await response.json();
        elmErrMess.innerHTML = ""
        if (result && result.errors) {
            const ojbErors = result.errors
            // Object.values(object) @return: array
            const arrErors = Object.values(ojbErors);
            // array.map(function(currentValue, index, arr), thisValue) @return An array
            // array.join(separator) @return: A string
            const errMess = arrErors.map(mess => `<p style="color:red; margin: 2px 0;">${mess}</p>`).join('');
            return elmErrMess.innerHTML = errMess
            //console.log(messErr)
            //console.log(Object.values(ojbErors))
            //console.log(Object.values(ojbErors).join(' '))
            //console.log(typeof result.errors)
        }
        if (result && result.status === 'success') {
            //console.log(result.username)
            if (result.token) {
                //console.log(result.token)
                localStorage.setItem('laravel_token', result.token);
                elmForm.reset();
                window.location.href = '/';
            }
        }
        // console.log(result)
        //console.log(response.status)

    } catch (e) {
        console.error(e)
        return elmErrMess.innerHTML = "Lỗi hệ thống vui lòng quay lại sau"
    }
}

document.addEventListener('DOMContentLoaded',
    handleSubmit
)

```

## check auth
```bash
// src/check-auth.js
(async function () {
    try {
        const response = await fetch('/api/check-auth', {
            headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json',
                'Authorization': 'Bearer ' + localStorage.getItem('laravel_token'),
            }
        });
        if (!response.ok) {
            window.location.href = '/login/'
        }
    } catch (err) {
        window.location.href = '/login/'
    }
})();

```