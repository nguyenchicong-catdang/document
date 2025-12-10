# tao vite_admin/adminVite.config.js
## tao thu muc
vite_admin
## tao file config
adminVite.config.js
## tao file root du an
```bash
// root.js
import path from 'node:path'

function root(strPath) {
    return path.join(__dirname,strPath)
}

export {root}
```
## code adminVite.config.js
```bash
// vite_admin/adminVite.config.js

import { defineConfig } from 'vite'
import { root } from '../root'
export default defineConfig({
    // ...
    root: root("vite_admin"),
})
```
## package.json
"dev-adminVite": "vite --config vite_admin/adminVite.config.js",
## npm run dev
"dev": "npm run dev-adminVite",