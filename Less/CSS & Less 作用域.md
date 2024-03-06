- CSS 本身没有作用域概念，它是全局性的。当你在一个 HTML 页面中使用 CSS 时，无论是通过 `<style>` 标签直接写在 HTML 中，还是通过 `<link>` 标签引入外部 CSS 文件，定义的样式规则默认作用于整个 HTML 文档，即如果你在不同的地方定义了相同选择器的样式，它们会相互影响，后定义的规则会根据 CSS 的层叠规则覆盖先定义的规则，除非有特定的权重差异，而这种全局性也导致样式冲突和难以维护等问题
- Less 作为 CSS 的预处理器，引入了局部作用域的概念，它允许使用嵌套规则来限制样式规则的作用范围。在 Less 中，变量和混合 Mixins 的作用域是由其所在的块级作用域决定的，即你可以在一个选择器内定义变量或混合，它们只会在该选择器或其子选择器内有效，从而避免了全局作用域带来的问题

```less
@primary-color: red;

.navbar {
    @primary-color: blue;
    background-color: @primary-color; // blue
    .button {
        background-color: @primary-color; // blue
    }
}

.button {
    background-color: @primary-color; // red
}
```

- 为解决 CSS 的全局作用域问题，现代前端开发引入了 CSS 模块化的概念，如 CSS Modules、Scoped CSS 等技术，它们通过自动生成唯一类名来实现样式的局部作用域，从而避免样式冲突

