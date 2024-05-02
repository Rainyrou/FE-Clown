TypeScript 不能直接在浏览器或 Node.js 环境中运行，它需被编译为 JavaScript，才能在这些环境中运行

1. 安装

```bash
npm i -g typescript
```

2. 编写

```TypeScript
const greet = (person: string) => "Hello " + person;
console.log(greet("World"));
```

3. 编译

```bash
tsc hello.ts
```

编译器进行类型检查 -> 通过 `tsconfig.json` 指定目标 JavaScript 版本，编译器会将 TypeScript 代码编译成相应版本的 JavaScript 代码
