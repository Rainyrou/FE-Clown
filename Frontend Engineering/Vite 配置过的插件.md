##### Vue & React 官方插件

对于 Vue 或 React 的项目，首先当然是官方插件 `@vitejs/plugin-vue` 或 `@vitejs/plugin-react` 以支持框架的特定语法和 HMR

`vite.config.js`

```JavaScript
import vue from '@vitejs/plugin-vue';

export default {
  plugins: [vue()]
};
```

##### ESLint

除了安装 VSCode 插件外，我们也可以通过 Vite 插件的方式在开发阶段进行 ESLint 扫描，以命令行的方式展示出代码中的规范问题，并能够直接定位到原文件

```bash
pnpm i vite-plugin-eslint -D
```

在 `vite.config.ts` 中接入：

```TypeScript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import viteEslint from 'vite-plugin-eslint';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), viteEslint()]
});
```

重新启动项目， ESLint 的错误已能实时反馈到命令行窗口中

##### Stylelint

当然，我们也可以直接在 Vite 中集成 Stylelint，社区中提供了 Stylelint 相应的 Vite 插件：

```bash
pnpm i vite-plugin-stylelint -D
```

`vite.config.ts`

```TypeScript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import viteEslint from 'vite-plugin-eslint';
import viteStylelint from 'vite-plugin-stylelint';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    react(),
    viteEslint(),
    viteStylelint({
      exclude: /windicss|node_modules/ // 对某些文件排除检查
    })
  ]
});
```

##### CSS-in-JS

CSS-in-JS 是一种使用 JavaScript 来写 CSS 的方法，通常在 React 和其他组件化框架中使用。使用 CSS-in-JS，你可以根据组件的 props 或 state 动态地生成和应用样式，使得样式和组件逻辑紧密地绑定在一起

- 选择器命名问题: 避免全局样式的冲突是 CSS-in-JS 的主要优势之一。大多数库通过生成唯一的类名来解决这个问题
- DCE (Dead Code Elimination): 在构建过程中，应该删除未使用的代码以减少最终的包大小
- 代码压缩: CSS 和 JavaScript 代码都应该在生产构建中被压缩
- 生成 SourceMap: SourceMap 可以帮助开发者定位到原始的源代码，而不是编译后的代码
- 服务端渲染 (SSR): 当使用 CSS-in-JS 与 SSR 一起时，需要确保在服务器上正确地生成和提取 CSS

而 `styled-components` 和 `emotion` 已提供了对应的 babel 插件来解决这些问题，我们在 Vite 中要做的就是集成这些 babel 插件

在命令行下输入：

```bash
pnpm install --save styled-components
pnpm install --save-dev babel-plugin-styled-components
pnpm install --save @emotion/react @emotion/styled
pnpm install --save-dev @emotion/babel-plugin
```

对于 emotion，需要单独加上 `jsxImportSource: "@emotion/react"` ，通过此包编译 emotion 中的 JSX 语法

![[1698064918670.png]]

##### SVG 组件

当 SVG 作为组件引入时，与传统的 `<img>` 标签方式相比，它可直接在组件中修改 SVG 的属性，代码也更具可读性，并能与 React 组件结构保持一致

不同的前端框架对 SVG 的处理有所不同，因此根据使用的框架选择相应的插件：

- Vue2：使用 `vite-plugin-vue2-svg`
- Vue3：使用 `vite-svg-loader`
- React：使用 `vite-plugin-svgr`

```bash
pnpm i vite-plugin-svgr -D
```

在 `vite.config.ts` 配置该插件：

![[1698148428520.png]]

在 `tsconfig.json` 中进行配置：

![[1698148568182.png]]

`App.tsx` 内容不变

`components/Header/index.tsx`

```TSX
import React, { useEffect } from 'react';
import { devDependencies } from '../../../package.json';
import styles from './index.module.scss';
import logoSrc from '@assets/imgs/vite.png';
import { ReactComponent as ReactLogo } from '@assets/icons/logo.svg';

export function Header() {
    return (<>
        <div className={`p-20px text-center ${styles.header}`}>
            <img className="m-auto mb-4" src={logoSrc} alt="" />
        </div>
        <ReactLogo />
    </>
    );
}
```

[vite, react-ts --- Module '"\*.svg"' has no exported member 'ReactComponent'. --- · Issue #44 · pd4d10/vite-plugin-svgr · GitHub](https://github.com/pd4d10/vite-plugin-svgr/issues/44)

`vite-env.d.ts`

```TypeScript
/// <reference types="vite/client" />
/// <reference types="vite-plugin-svgr/client" />

declare module "*.svg" {
  import * as React from "react";
  export const ReactComponent: React.FunctionComponent<
    React.SVGProps<SVGSVGElement> & { title?: string }
  >;
  const src: string;
  export default src;
}
```

似乎无解 ┭┮﹏┭┮

![[04c505ffe04e2e87f8833af9a2dd6ba.png]]

但好在代码上不再爆红，也算有点进步，说明 `vite-env.d.ts` 有点效果：

![[1698152853266.png]]

暂时放一放...

##### 图片压缩

图片资源往往是项目产物体积的大头，图片体积大小会影响页面加载速度和用户体验。在 JavaScript 领域有一个知名的图片压缩库 imagemin，作为一个底层的压缩工具，前端的项目经常基于它来进行图片压缩，比如 Webpack 中大名鼎鼎的 `image-webpack-loader`。社区中也有了开箱即用的 Vite 插件 `vite-plugin-imagemin`，首先安装它:

```bash
pnpm i vite-plugin-imagemin -D
```

安装时出现问题...

![[1698202334661.png]]

去官方文档和其他地方查了大半天，也试过用 npm 和 yarn 安装这个包，但最终的解决方案是改下 `package.json` 配置：

![[1698202864177.png]]

![[1698202927478.png]]

在 `vite.config.ts` 中引入并配置：

![[1698202411782.png]]

在命令行执行：

```bash
pnpm run build
```

![[1698202962504.png]]

成功(●'◡'●)

##### 底层原理

Vite 插件的底层原理建立在 Rollup 插件系统之上。因为 Vite 在生产构建中使用 Rollup，所以许多 Rollup 插件在 Vite 中也可以直接使用。Vite 插件可以返回具有多个钩子的对象，这些钩子会在处理源码时被调用。例如：

- `resolveId`：用于解析模块 ID
- `load`：用于读取文件内容
- `transform`：用于转换文件内容

插件可以利用这些钩子执行任务，如代码转换、文件解析和其他自定义功能

##### 实战

一个简单的 Vite 插件实现例子可能是这样的：

```JavaScript
// my-plugin.js
export default function myPlugin(options) {
  return {
    name: 'my-custom-plugin', // 插件名称
    resolveId(source) {
      // 自定义解析逻辑
    },
    load(id) {
      // 自定义加载逻辑
    },
    transform(code, id) {
      // 自定义代码转换逻辑
    }
  };
}
```

在 `vite.config.js` 中使用这个插件：

```JavaScript
import myPlugin from './my-plugin.js';

export default {
  plugins: [myPlugin()]
};
```
