# document.cookie
const cookies = document.cookie
## lấy mảng cookie
const arrCookies = document.cookie.split('; ');
## Tạo mảng các mảng con [[key, value], [key, value]]
const cookiePairs = arrCookies.map(item => item.split('='));
## Chuyển thành đối tượng Map
const cookieMap = new Map(cookiePairs);
## sử dụng
console.log(cookieMap.get('XSRF-TOKEN'));
## Xử lý Decode Token
const XSRF_TOKEN = decodeURIComponent(cookieMap.get('XSRF-TOKEN'))