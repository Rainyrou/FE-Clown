`<script>` 用于引入外部 JavaScript 文件

- 在无 `async` 或 `defer` 的情况下，JavaScript 同步下载执行，阻塞 HTML 解析
- `async` 和 `defer` 允许浏览器异步下载脚本，不阻塞 HTML 解析
- `async` 使脚本下载后立即执行，但不能保证按照它们在文档中的顺序执行，而 `defer` 保留脚本在 HTML 中的执行顺序，并在文档解析完成后执行它们
- `async` 适用于独立的脚本，这些脚本间无执行顺序依赖，`defer` 适用于依赖于 DOM 或其他脚本执行顺序的脚本
