1. 浏览器不支持

解决方案：HTML5 `picture` 允许为不同情况指定多个源图片，浏览器选择它支持的第一个图片格式进行加载

```HTML
<picture>
  <source srcset="image.webp" type="image/webp">
  <source srcset="image.jpg" type="image/jpeg">
  <img src="image.jpg" alt="Description" style="width: auto;">
</picture>
```

2. 图片路径或 URL 拼写错误或未正确上传到服务端

解决方案：检查图片路径或 URL 是否正确，确保图片已被正确上传到服务端预期的位置

3. 图片在传输或保存过程中损坏

解决方案：重新生成或下载 WebP 图片，并确保在上传输或保存过程中不损坏

4. 网络问题导致图片加载失败

解决方案：检查网络，刷新页面

5. CORS 限制

解决方案：配置允许跨域或将图片移至同一域名下

6. 服务端未配置正确的 MIME 类型 `image/webp`

解决方案：修改服务端配置


