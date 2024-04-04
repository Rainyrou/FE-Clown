修改本地 hosts 文件可解决跨域问题，让域名不经过 DNS 服务器直接指向特定 IP 地址。当你通过修改 hosts 文件，将公司内网的域名指向 `127.0.0.1` 本机地址或其他特定 IP 时，你其实是在伪装域名对应的服务端地址，当浏览器尝试访问该域名时，实际上访问的是本机或特定的服务器。若配置后的地址与请求的前端页面处于同一源下，即可绕过浏览器的同源策略限制

1. 打开 `C:\Windows\System32\drivers\etc\hosts` 文件，将 `company.internal` 解析到 `127.0.0.1`，直接在 hosts 文件中添加：

```
127.0.0.1 company.internal
```

3. 重启浏览器或在命令提示符执行` ipconfig /flushdns` 清除 DNS 缓存

###### 注意事项

- 修改 hosts 文件只适合开发和测试环境，不建议在生产环境中使用
- hosts 文件只需列出 IP 或域名，无需给出端口
* hosts 文件不支持通配符，我们无法通过配置自动匹配多个子域名，每个需要解析的子域名都要明确列出

```
127.0.0.1 example.company.internal
127.0.0.1 example2.company.internal
```

