# npm i @vitejs/plugin-react
npm i @vitejs/plugin-react
# import react from '@vitejs/plugin-react'
import react from '@vitejs/plugin-react'
# plugins: [react()],
```bash
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
})
```