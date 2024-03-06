1. `base`：指定生产环境下的基础路径，用于确定项目中静态资源的引用路径
2. `outDir`：指定构建产物输出目录
3. `assetsDir`：指定静态资源文件输出目录
4. `minify`：启用 & 禁用压缩或指定使用的压缩工具 `'terser'` | `'esbuild'` | `false`
5. `sourcemap`：生成源码映射文件
6. `rollupOptions`：直接传递给 Rollup 的配置选项，允许在构建中利用 Rollup 的高级配置
7. `css.codeSplit`：控制是否对 CSS 代码进行代码分割，默认情况下，Vite 会为每个异步加载的 JavaScript chunk 生成相应的 CSS 文件

