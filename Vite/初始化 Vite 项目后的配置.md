别名 + 服务器选项 + 插件 + CSS 预处理器 + 构建选项 + 环境变量和模式

`vite.config.js`

```JavaScript
import { defineConfig } from "vite";
import path from "path";

export default defineConfig({
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
  server: {
    port: 3000,
    proxy: {
      "/api": {
        target: "https://github.com/Rainyrou/FE-Clown",
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ""),
      },
    },
  },
  plugins: [vue()],
  css: {
    preprocessorOptions: {
      additionalData: `$injectedColor: orange;`,
    },
  },
  build: {
    outDir: "dist",
    sourcemap: true,
    minify: "terser",
    terserOptions: {
      compress: {
        drop_console: true,
      },
    },
  },
});
```

通过在项目根目录下创建 `.env` 和 `.env.production` 等文件来定义环境变量

```
// .env 
VITE_APP_TITLE=My App

// 在项目中使用 
console.log(import.meta.env.VITE_APP_TITLE);
```