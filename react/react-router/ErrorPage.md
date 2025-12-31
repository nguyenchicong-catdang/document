# useRouteError bắt lỗi
## routes.js
```bash
{
                                path: ':id/edit',
                                Component: PostsEdit,
                                loader: postsEditLoader,
                                errorElement: <ErrorPage />
                            },
```
## loader
```bash
});
        if (response.ok) {
            return await response.json();
            //console.log(result)

        } else {
            //console.error(response.statusText)
            // Exception -> err
            throw new Response(response.statusText, { status: response.status })
        }
    } catch (err) {
        if (err instanceof Response) {
            throw err
        }
        // Nếu là lỗi kết nối (Network error, rớt mạng...)
        throw new Response("Lỗi kết nối hệ thống", { status: 500 });
    }
```
## ErrorPage -> component
errorElement: <ErrorPage />

```bash
import { useLocation, useRouteError } from "react-router"

export default function ErrorPage() {
    const error = useRouteError();
    const location = useLocation();
    console.error(error, location)
  return (
      <div>
          <h1>Rất tiếc! Đã có lỗi xảy ra.</h1>
          <p>{error.data || "Lỗi mạng hoặc server không phản hồi"}</p>
          
    </div>
  )
}

```
### ErrorPage -> tham khao
```bash
import { useRouteError } from "react-router-dom";

// Component con để hiển thị hình ảnh minh họa tương ứng với mã lỗi
const ErrorIllustration = ({ status }) => {
    if (status === 404) return <img src="/not-found.svg" alt="Không tìm thấy" />;
    if (status === 500) return <img src="/server-error.svg" alt="Lỗi hệ thống" />;
    return <img src="/generic-error.svg" alt="Lỗi" />;
};

// Component con cho nút quay lại
const BackButton = () => (
    <button onClick={() => window.location.href = '/'}>Quay lại trang chủ</button>
);

export default function ErrorPage() {
    const error = useRouteError();

    return (
        <div className="error-layout">
            <ErrorIllustration status={error.status} />
            <h1>{error.status === 404 ? "Trang này không tồn tại" : "Có lỗi hệ thống"}</h1>
            <p>{error.statusText || error.data}</p>
            <BackButton />
        </div>
    );
}
```