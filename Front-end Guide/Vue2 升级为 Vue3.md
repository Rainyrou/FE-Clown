1. 对于本项目，考虑逐步迁移的策略
2. TailwindCSS 本身与 Vue 版本兼容性问题不大，主要关注点在于集成方式。确保在 Vue 3 项目中正确配置 TailwindCSS，并根据文档更新配置文件如 `tailwind.config.js`
3. 升级到 Vue 3 时，将 Element UI 替换为 Element Plus，使用官方提供的插件或工具来实现按需引入，访问 Element Plus 的官方文档，逐一对照，找出那些已更名或弃用的组件，查看迁移指南和新的 API 文档

- 如 Element UI 中的 `<el-table>` 组件在 Element Plus 中的用法有所改变，需根据 Element Plus 文档更新用法，调整属性并替换为新的组件
- 根据 Vue 3 的事件绑定和 `v-model` 用法，更新组件的事件处理代码。原来使用 `v-model` 双向绑定数据到 Element UI 组件，需要调整为 Vue 3 推荐的 `modelValue` 和 `update:modelValue` 模式

```JavaScript
// Vue 2 + Element UI
<el-input v-model="inputValue"></el-input>

// Vue 3 + Element Plus
<el-input :modelValue="inputValue" @update:modelValue="val => inputValue = val"></el-input>
```

4. 确保 Element Plus 和 TailwindCSS 的样式共存而不冲突，通过 CSS 作用域、CSS Modules 和 Vue 3 的 `<style scoped>` 来隔离组件样式。同时利用 Element Plus 提供的主题定制功能，调整与 TailwindCSS 更匹配的颜色、边距等样式变量

###### Element Plus 不能满足业务需求时

1. 扩展现有组件：若其组件能满足基本需求，但缺少一些功能或样式，可以通过 Slots 或封装一个新组件来扩展它。通过此方式可保留 Element Plus 组件的基础功能，同时添加自定义的功能或样式
2. 使用第三方组件：若其无所需组件，寻找其他组件库或独立的 Vue 组件，许多问题可能已有现成的解决方案
3. 自定义组件：从头设计组件的逻辑和样式，需要花点时间琢磨，但其提供了最大的灵活性
4. 为 Element Plus 贡献代码（**高阶玩法**）

###### Element Plus 自定义样式

1. 使用 CSS 覆盖

在你的全局 `CSS` 文件覆盖 Element Plus 组件的默认样式，由于 CSS 的特性，使用更具体的选择器或 `!important` 规则来确保自定义样式覆盖组件的默认样式

```CSS
/* 全局CSS文件 */
.el-button {
  background-color: #409eff !important; /* 改变按钮的背景颜色 */
  color: white !important; /* 改变按钮文字颜色 */
}
```

2. 使用 SCSS 变量（官方推荐）

通过覆盖其 SCSS 变量来自定义主题

```bash
npm i sass sass-loader deepmerge -D
```

在项目的入口文件 `main.ts` 之前导入自定义主题文件：

```TypeScript
import './custom-theme.scss';
import ElementPlus from 'element-plus';
import 'element-plus/lib/theme-chalk/index.css';
```

在 `custom-theme.scss` 文件中自定义变量来覆盖默认的样式变量：

```SCSS
$--color-primary: teal;
@import "~element-plus/packages/theme-chalk/src/index";
```

3. 使用 JavaScript API 动态修改样式（没用过）

通过修改组件的样式属性或类属性来实现：

```JavaScript
<template>
  <el-button :class="{'custom-class': isCustom}" @click="toggleCustom">Toggle Custom Style</el-button>
</template>

<script>
export default {
  data() {
    return {
      isCustom: false,
    };
  },
  methods: {
    toggleCustom() {
      this.isCustom = !this.isCustom;
    },
  },
};
</script>

<style>
.custom-class {
  background-color: #409eff !important;
  color: white !important;
}
</style>
```

###### Element Plus 按需加载

Element Plus 支持按需加载组件，可以只导入所需组件，而非整个库，减少最终打包的文件大小。按需加载可通过 babel 插件 `babel-plugin-import` 实现，它自动将全量导入语句转换为只导入所需的组件

1. 安装依赖

```bash
npm i babel-plugin-import --save-dev
```

2. 配置 Babel

在项目中找到 Babel 配置文件 `babel.config.js`，添加配置：

```JavaScript
module.exports = {
  plugins: [
    [
      "import",
      {
        libraryName: "element-plus",
        customStyleName: (name) => {
          return `element-plus/lib/theme-chalk/${name}.css`;
        },
      },
    ],
  ],
};
```

3. 按需导入

```JavaScript
import { ElButton, ElSelect } from 'element-plus';
```

###### 关于 TailwindCSS

TailwindCSS 是一个实用工具优先的原子化 CSS 框架，它提供了构建响应式布局的灵活性和速度

1. 学习曲线

对新手来说，TailwindCSS 的学习曲线可能比传统 CSS 或其他 CSS 框架更为陡峭。因为它引入了大量的实用类，初学者需要花时间去记忆这些类的名称和功能。虽然存在上述情况，但一旦掌握它，你的开发效率能得到显著提高

2. HTML 文件大小

使用 TailwindCSS 导致 HTML 文件大小增加，因为你需要在 HTML 标签中添加许多实用类。这一点确实存在，但通过使用 PurgeCSS 等工具可在构建过程中删除未使用的 CSS，显著减少最终的文件大小

3. 自定义样式

虽然 TailwindCSS 提供了高度的可定制性，但对于一些特定设计或复杂样式，需要编写更多自定义 CSS 或实用工具类，
TailwindCSS 的配置文件 `tailwind.config.js` 提供了强大的自定义能力，允许开发者扩展主题、添加实用工具类等，这些都有助于解决自定义样式的需求

4. 设计系统

TailwindCSS 鼓励在标记中直接使用实用类，这限制了设计系统的抽象和封装。虽然这种方式确实减少了样式的抽象层，但它同时也提供了快速原型开发和迭代的灵活性。对于更加抽象的项目，可结合使用组件库或 CSS 预处理器来构建

5. 依赖构建工具

TailwindCSS 需要一个构建过程来优化最终的 CSS 文件，这增加了项目的复杂性。尽管如此，但现代前端开发中构建工具的使用已非常普遍。TailwindCSS 与这些工具的集成并不复杂，且带来的性能优化和可维护性远超其配置成本
