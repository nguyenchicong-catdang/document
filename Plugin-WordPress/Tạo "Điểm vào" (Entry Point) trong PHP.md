# Tạo "Điểm vào" (Entry Point) trong PHP

```php
function render_sync_page() {
    // Đây là điểm vào cho React
    echo '<div id="my-react-app"></div>';
}
```
## React vào WordPress
```php
add_action('admin_enqueue_scripts', function($hook) {
    // Chỉ load script nếu đang ở trang của plugin
    if ($hook !== 'toplevel_page_laravel-cache-sync') return;

    // Đăng ký file JS đã build từ React của bạn
    wp_enqueue_script(
        'my-react-plugin-js',
        plugins_url('build/index.js', __FILE__),
        array('wp-element', 'wp-api-fetch'), // wp-element chính là React của WP
        '1.0',
        true
    );

    // Truyền dữ liệu từ PHP sang React (như Nonce, URL Ajax)
    wp_localize_script('my-react-plugin-js', 'wpData', array(
        'ajax_url' => admin_url('admin-ajax.php'),
        'nonce'    => wp_create_nonce('wp_rest')
    ));
});
```

## Cấu trúc Component React (Phía Client)

```js
import { render } from '@wordpress/element'; // WordPress bọc React vào đây

const MyPluginApp = () => {
    const handleSync = () => {
        // Dùng apiFetch (thay cho fetch thuần) để tự động xử lý Nonce của WP
        wp.apiFetch({ 
            path: '/wp/v2/posts', // Hoặc gọi tới admin-ajax.php qua wpData.ajax_url
            method: 'POST' 
        }).then(res => console.log(res));
    };

    return (
        <div className="react-admin-wrapper">
            <h1>Laravel Redis Sync Dashboard</h1>
            <button onClick={handleSync} className="button button-primary">
                Clear Redis Now
            </button>
        </div>
    );
};

// Render ứng dụng vào div có ID đã tạo ở Bước 1
render(<MyPluginApp />, document.getElementById('my-react-app'));
```

# Sử dụng @wordpress/scripts (Cách chuẩn của WP)
```bash
npm init -y
npm install @wordpress/scripts --save-dev
```
## Cấu hình package.json
```json
"scripts": {
  "start": "wp-scripts start",
  "build": "wp-scripts build"
}
```

> Cài đặt Vite React

## Cấu hình vite.config.js
```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  build: {
    // Xuất file vào thư mục build của plugin
    outDir: 'build',
    rollupOptions: {
      input: 'src/main.jsx',
      // Không đóng gói React vì WP đã có
      external: ['react', 'react-dom'],
      output: {
        globals: {
          react: 'React',
          'react-dom': 'ReactDOM',
        },
        entryFileNames: `[name].js`,
      },
    },
  },
  server: {
    // Quan trọng cho HMR
    origin: 'http://localhost:5173',
    cors: true,
  },
});
```

## Xử lý Logic PHP để hỗ trợ HMR
```php
add_action('admin_enqueue_scripts', function($hook) {
    if ($hook !== 'toplevel_page_laravel-cache-sync') return;

    $is_dev = defined('SCRIPT_DEBUG') && SCRIPT_DEBUG;

    if ($is_dev) {
        // Load trực tiếp từ Vite Server để dùng HMR
        wp_enqueue_script('vite-client', 'http://localhost:5173/@vite/client', [], null, true);
        wp_enqueue_script('my-react-app', 'http://localhost:5173/src/main.jsx', ['vite-client'], null, true);
        
        // Cần thêm type="module" cho script của Vite
        add_filter('script_loader_tag', function($tag, $handle) {
            if (in_array($handle, ['vite-client', 'my-react-app'])) {
                return str_replace(' src', ' type="module" src', $tag);
            }
            return $tag;
        }, 10, 2);
    } else {
        // Load file đã build (Production)
        wp_enqueue_script('my-react-app', plugins_url('build/main.js', __FILE__), ['wp-element'], '1.0', true);
    }
});
```

## Cách setup để npm start chạy đúng với WordPress Admin
```php
add_action('admin_enqueue_scripts', function() {
    // Đường dẫn đến file index.asset.php (do wp-scripts tự sinh ra)
    // File này chứa danh sách các dependency (như wp-element) và version
    $asset_file = include( plugin_dir_path( __FILE__ ) . 'build/index.asset.php');

    wp_enqueue_script(
        'my-react-app',
        plugins_url( 'build/index.js', __FILE__ ),
        $asset_file['dependencies'], // Tự động load ['wp-element', 'wp-polyfill', ...]
        $asset_file['version'],
        true
    );
});
```