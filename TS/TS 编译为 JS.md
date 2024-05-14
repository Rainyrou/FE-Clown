TypeScript 不能直接在浏览器或 Node.js 环境中运行，它需被编译为 JavaScript，才能在这些环境中运行

1. TypeScript 编译器解析 `.ts` 或 `.tsx` 文件，将源代码转换为 AST，它是一个深层嵌套的对象，表示源代码的结构，其中包括语法元素及其属性如变量声明、函数调用、类和接口等
2. TypeScript 编译器进行类型检查，验证类型的兼容性，检查所有类型注解和推断是否正确，若发现类型错误，编译器输出错误并停止编译
3. TypeScript 编译器将 AST 转换为与目标 JavaScript 版本兼容的 JavaScript 代码，在此过程中，编译器应用多种转换来处理不被目标 JavaScript 版本支持的 TypeScript 特性如将 ES6 的类转换为 ES5 的函数和原型，同时移除所有的 TypeScript 类型注解，因为 JavaScript 为动态类型
4. TypeScript 编译器将转换后的 AST 再转换为可执行的 JavaScript 代码字符串，并将它们写入到 `.js` 文件中，若配置了 sourceMap，TypeScript 编译器生成一个 sourceMap 文件，该文件映射编译后的 JavaScript 代码回原始的 TypeScript 代码，便于后续的调试
