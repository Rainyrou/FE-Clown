>[!todo] To do 
>全文背诵

在多人协作的大型项目中，不同的人有不同的代码风格。为提高代码的可读性和可维护性，团队通常会制定一套代码规范，用来统一团队代码风格，提高代码质量，甚至可以在开发阶段提前规避掉一些语法错误。但仅有规范本身不够，我们需要自动化工具来保证规范的落地，把代码规范检查这件事情交由机器完成，开发者只需要专注应用逻辑本身

- ESLint：一个用于检查和修复 JavaScript 代码的工具，它可以识别和修复代码中的问题，确保代码的质量和一致性
- Prettier：一个代码格式化工具，可以自动格式化代码以满足特定的风格
- Stylelint：一个用于检查 CSS/SCSS/Less 代码的工具，确保样式代码的一致性
- Commitlint：检查 git 提交消息的工具，确保它们遵循一致的格式
- husky：允许开发者在 git 钩子中运行自定义脚本，如在提交之前运行 lint 工具
- lint-staged：只检查 git 暂存区中的文件，这意味着只有实际修改的文件会被 lint，而不是整个代码库
- VSCode 插件：可以在你写代码时自动运行 Lint 工具，从而实时提示问题
- Vite 生态：与 Vite 结合，确保开发过程中代码的质量

### ESLint

#### 初始化

ESLint 是一个 JavaScript 代码静态检查工具，它的核心原理是通过解析 JavaScript 代码生成 AST，然后使用预定义的规则来检查 AST 的各个部分是否满足条件，检查代码的风格和质量问题。它可以帮助开发者在代码编写阶段发现潜在问题，而不是等到运行时。Eslint 是一个非常成功的开源项目，属于前端项目中 Lint 工具的标配。ESLint 的行为是基于一系列的配置规则，开发者可以选择使用社区提供的预定义规则集，如 Airbnb 或 Google 的 JavaScript 规范，也可以根据项目需要自定义规则

```bash
pnpm i eslint -D
npx eslint --init
```

![[1698108349420.png]]

ESLint 会帮我们自动生成 `.eslintrc.js` 配置文件。需要注意的是，在上述流程中我们并没有用 npm 安装依赖，需要手动安装：

```bash
pnpm i eslint-plugin-react@latest @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest -D
```

#### 核心配置

##### parser

ESLint 底层默认使用 Espree。Espree 可以理解和解析标准的 ECMAScript 语法，它在其早期版本是完全独立的，但后来决定基于 Acron 来增强其功能和兼容性。然而，尽管 Acron 非常强大，但它不支持 TypeScript。因此需要引入其他解析器来解析 TS。为解决这一问题，社区开发了 `@typescript-eslint/parser`。它可以解析 TypeScript 代码，并将其转换为 ESLint 和 Espree 可以理解的 AST 格式（即 Estree 格式）。通过这种方式，ESLint 可以像检查普通的 JavaScript 代码一样检查 TypeScript 代码。

##### parserOptions

ecmaVersion：

- 指定 ECMAScript 版本。如使用 ES6/ES2015 语法只需设置 `ES6` 或者 `ES2015` ,也可以简单地设置为 `latest`，使用最新语法

sourceType：

- 指定代码是作为单个脚本（`script`）还是作为模块（`module`）来解析。使用 ES Module 应设置为 `module`

ecmaFeatures：

- 这是一个对象，可以在其中指定额外的 ECMAScript 特性。如果代码中使用了 JSX，那么你需要在对象中设置 `{ jsx: true }`

##### rules

你可以在 rules 里启用、禁用或修改任何 ESLint 规则

```JavaScript
rules: {
  "no-cond-assign": ["error", "always"] // key 为规则名，value 配置内容
}
```

这里的 `"no-cond-assign"` 指的是禁止在条件语句中使用赋值语句，`"always"` 选项指始终不允许在条件语句中使用赋值

数组中的 `"error"` 是该规则的严格性：

- `"off"` 或 `0` 表示关闭规则
- `"warn"` 或 `1` 表示违规时发出警告
- `"error"` 或 `2` 表示违规时报告错误

##### plugins

插件允许你为 ESLint 添加新规则或修改现有规则

```JavaScript
plugins: ['@typescript-eslint']
```

这意味着代码已经包含了一个名为 `@typescript-eslint` 的插件。这个插件提供了一些与 TypeScript 代码相关的额外规则。但是，添加插件只是拓展了 ESLint 本身的规则集，但 ESLint 默认没有开启这些规则的校验。要使用插件中的规则，你需要在 `rules` 中进行配置

```JavaScript
rules: {
  '@typescript-eslint/ban-ts-comment': 'error',
  '@typescript-eslint/no-explicit-any': 'warn',
}
```

- `ban-ts-comment` 规则将在你的代码中禁止使用 TypeScript 的 `// @ts-ignore` 之类的注释
- `no-explicit-any` 规则会警告你不要在 TypeScript 中使用 `any` 类型

##### extends

extends 字段使你可以继承其他的 ESLint 配置，而不必每次都在项目中重新定义相同的规则

```JavaScript
"extends": [
  "eslint:recommended",
  "standard",
  "plugin:react/recommended",
  "plugin:@typescript-eslint/recommended"
]
```

- `"eslint:recommended"`：这是 ESLint 本身提供的一套核心规则，它包括了所有常见的 JavaScript 错误和最佳实践
- `"standard"`：这是一个受欢迎的 JavaScript 标准样式指南的 ESLint 配置
- `"plugin:react/recommended"` 和 `"plugin:@typescript-eslint/recommended"`：这些是从特定插件中导入的推荐规则集

通过 extends 字段可自动开启插件中的推荐规则

##### env & globals

env 定义预期的运行环境。基于选择的环境，ESLint 会知道哪些全局变量是可接受的，从而不会在你使用这些变量时给出警告

```JavaScript
"env": {
  "browser": true,
  "node": true
}
```

这意味着你的代码既能在浏览器中运行，也可以在 Node.js 中运行。所以 ESLint 会预期 `window` 或 `document`（浏览器的全局变量）和 `process` 或 `Buffer`（Node.js 的全局变量）这些都是合法的

globals 定义特定的全局变量，这样 ESLint 在碰到这些变量时不会认为它们是未定义的

```JavaScript
"globals": {
  "$": "readonly",
  "jQuery": "readonly"
}
```

这里我们定义了两个全局变量 `$` 和 `jQuery`，它们是 jQuery 库中的常见变量。我们设置它们为 `"readonly"`，这意味着 ESLint 期望这些变量是只读的，如果在代码中尝试修改它们，ESLint 会给出警告

#### Prettier

尽管 ESLint 本身具备自动格式化代码的功能(`eslint --fix`)，但其主要功能在于代码风格的检查和提示，而 Prettier 在格式化代码方面更为出色，因此我们选择将两者结合起来使用

```bash
pnpm i prettier -D
```

在项目根目录新建 `.prettierrc.cjs` 配置文件：

```JavaScript
module.exports = {
  printWidth: 80, // 一行的字符数，如果超过会进行换行，默认为80
  tabWidth: 2, // 一个tab代表几个空格数，默认为80
  useTabs: false, // 是否使用tab进行缩进，默认为false，表示用空格进行缩减
  singleQuote: true, // 字符串是否使用单引号，默认为false，使用双引号
  semi: true, // 行位是否使用分号，默认为true
  trailingComma: 'none', // 是否使用尾逗号，有三个可选值"<none|es5|all>"
  bracketSpacing: true // 对象大括号直接是否有空格，默认为true，效果：{ foo: bar }
};
```

接下来我们将 `Prettier` 集成到现有的 `ESLint` 工具中，先安装两个工具包：

```bash
pnpm i eslint-config-prettier eslint-plugin-prettier -D
```

`eslint-config-prettier` 会覆盖那些与 Prettier 冲突的 ESLint 规则
`eslint-plugin-prettier` 使 Prettier 可以作为 ESLint 的一个规则运行，让 Prettier 来接管 `eslint --fix` 即修复代码的能力

在  `.eslintrc.cjs`  配置文件中接入 Prettier 相关工具链

```JavaScript
module.exports = {
  env: {
    browser: true,
    es2021: true
  },
  extends: [
    'eslint:recommended',
    'plugin:react/jsx-runtime',
    'plugin:@typescript-eslint/recommended',
    'prettier',
    'plugin:prettier/recommended'
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    },
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  plugins: ['react', '@typescript-eslint', 'prettier'],
  rules: {
    'prettier/prettier': 'error',
    quotes: ['error', 'single'],
    semi: ['error', 'always'],
    'react/react-in-jsx-scope': 'off'
  }
};
```

再配个 `.eslintignore` 和 `.prettierignore`，具体见 GitHub 仓库

现在我们见证一下 ESLint + Prettier 的强大威力，在  `package.json`  中定义一个脚本：

![[1698114530425.png]]

在命令行中执行：

```bash
pnpm run lint:script
```

这样我们就完成了  ESLint 的规则检查以及  Prettier 的自动修复。不过每次执行这个命令未免会有些繁琐，我们可以在 VSCode 中安装 ESLint 和 Prettier 插件，并且在设置区中开启 Format On Save

![[1698115262135.png]]

`.eslintrc.cjs` 一片红，暂时改为 `.js` 格式

按 Ctrl + S 保存代码时（记得要启动项目，不然看不到效果），Prettier 会自动修复代码格式

#### 在 Vite 中接入 ESLint

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

#### Stylelint

Stylelint 是一个样式检查 Lint 工具，用来帮助开发者避免语法错误，统一代码风格

与 ESLint 类似，Stylelint 有自己的一套核心规则，且允许通过插件机制来扩展这些规则。在代码规范检查方面，Stylelint 已做到足够专业，但在格式化方面，仍需要结合 Prettier 一起使用

首先安装 Stylelint 以及相应的工具套件：

```bash
pnpm i stylelint stylelint-prettier stylelint-config-prettier stylelint-config-recess-order stylelint-config-standard stylelint-config-standard-scss -D
```

`.stylelintrc.js` 是 Stylelint 的配置文件，配置如下：

```JavaScript
module.exports = {
  plugins: ['stylelint-prettier'], // 注册 Stylelint 的 Prettier 插件
  // 继承一系列规则集合
  extends: [
    'stylelint-config-standard',
    'stylelint-config-standard-scss',
    'stylelint-config-recess-order',
    'stylelint-config-prettier', // 接入 Prettier 规则
    'stylelint-prettier/recommended'
  ],
  rules: {
    'prettier/prettier': true // 开启 Prettier 自动格式化功能
  }
};
```

1. plugins: 注册插件的地方
2. extends: 指定要继承的规则集
3. rules: 定义或重写规则的地方，Stylelint 中的 `rules` 的配置与 ESLint 有所不同：

- null: 关闭规则
- 一个简单值: 开启规则但不做过多定制，具体的值（如 true、字符串）会因规则而异
- 一个数组: 数组的第一个元素通常是一个简单值，表示开启或关闭规则；第二个元素提供了这个规则的详细配置

接下来我们将 Stylelint 集成到项目中，回到  `package.json`  中：

![[1698129755449.png]]

命令行下执行 `pnpm run lint:style` 即可完成样式代码的规范检查和自动格式化。也可以在 VSCode 中安装 `Stylelint` 插件，这样能够在开发阶段即时感知到代码格式问题，提前进行修复

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

#### Husky & lint-staged

##### 提交前的代码 Lint 检查

尽管在开发过程中有 ESLint、Prettier 和 Stylelint 这些工具的提示，但实际上这也只是将问题提前暴露，并不能确保所有开发者都会遵循代码规范。因此，为了确保代码规范的执行，可以在代码提交时做卡点检查，拦截  `git commit`  命令，只有当代码满足规范时才允许提交

Husky 是一个可以轻松使用 Git 钩子的工具。它可以帮助我们在执行如 `git commit` 和 `git push` 之类的命令前运行一些预定义的脚本，如代码检查。首先在命令行执行：

```bash
pnpm i husky -D
```

1. 初始化 Husky

```bash
git init
npx husky install
```

在 `package.json` 生成以下东东：

![[1698131707992.png]]

2. 添加 Husky 钩子，在命令行执行：

```bash
npx husky add .husky/pre-commit "npm run lint"
```

在 `.husky` 目录中你可以看到名为 `pre-commit` 的文件，里面包含了  `git commit` 前要执行的脚本。现在，当你执行  `git commit`  前，会先执行  `npm run lint` 脚本，通过 Lint 检查后才会正式提交代码记录

不过，刚才我们直接在 Husky 的钩子中执行  `npm run lint`，这会产生一个额外的问题: Husky 中每次执行 `npm run lint` 都对仓库中的代码进行全量检查，也就是说，即使某些文件并没有改动，也会走一次 Lint 检查，当项目越来越大的时候，这会严重影响开发体验

而 lint-staged 就是用来解决上述问题的，它只会检查 Git 暂存区中的文件。先安装一下对应的 npm 包：

```bash
pnpm i -D lint-staged
```

然后在  `package.json` 中添加如下的配置：

![[1698132235910.png]]

在 `.husky/pre-commit` 脚本中，替换 `npm run lint` 为 `npx --no -- lint-staged`，这样 Husky 在每次 `git commit` 前都会运行 lint-staged

##### 提交时的 commit 信息规范

先安装工具库，在命令行执行：

```bash
pnpm i commitlint @commitlint/cli @commitlint/config-conventional -D
```

我们一般直接使用 `@commitlint/config-conventional` 规范集即可，它所规定的 commit 信息结构如下：

```git
// type 指提交的类型 subject 指提交的摘要信息
<type>: <subject>
```

常用的  `type`  值包括：

- `feat`: 添加新功能
- `fix`: 修复 Bug
- `chore`: 一些不影响功能的更改
- `docs`: 专指文档的修改
- `perf`: 性能方面的优化
- `refactor`: 代码重构
- `test`: 添加一些测试代码

接下来我们将 commitlint 的功能集成到 Husky 的钩子当中，在命令行执行：

```bash
npx husky add .husky/commit-msg "npx --no-install commitlint -e $HUSKY_GIT_PARAMS"
```

你可以发现在 `.husky` 目录下多了 `commit-msg` 文件，表示 commitlint 命令已经成功接入到 husky 的钩子当中

