Less 是一种动态样式语言，它扩展 CSS 的功能，通过引入变量、混合 mixins、函数等特性，使得 CSS 更加灵活高效和易于维护。Less 可以在客户端或服务器端处理，最终被编译成原生 CSS 以供浏览器使用

- 变量

```less
@primary-color: #4D9FEC;
body {
  color: @primary-color;
}
```

- 混合允许将一组 CSS 属性从一个规则集包含或混合到另一个规则集中

```less
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}

#menu a {
  .bordered;
}
```

- 嵌套

```less
#header {
  h1 {
    font-size: 26px;
    font-weight: bold;
  }
  p {
    font-size: 12px;
    a {
      text-decoration: none;
    }
  }
}
```

- 函数

```less
@width: 10px;
.calc {
  width: @width + 10;
}
```

###### 编译过程

Less 编译器首先解析 `.less` 文件，分析其中的语法，接着编译器执行相应的运算和函数，处理条件语句和循环等。最后，编译器将处理后的结果转换成原生 CSS 代码

Less 的编译过程可以通过不同的方式进行：

- 客户端处理：在 HTML 文件中引入 `LESS.js` 脚本，它会在浏览器实时处理 `.less` 文件，适用于开发环境
- 服务端处理：通过 Node.js 等环境使用 Less 编译器预编译 `.less` 文件为 `.css` 文件，适用于生产环境
- 构建工具：使用 Webpack 等构建工具和相应的 loader 或插件编译 Less
