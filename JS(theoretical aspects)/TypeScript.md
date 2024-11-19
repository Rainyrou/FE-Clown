JavaScript 是一种非常灵活的语言，它不要求变量有明确的类型定义，允许动态地访问和修改属性，此种灵活性在小型项目中可以提高我们的开发效率，然而随着项目规模的增长及变量数量和数据类型的增加，此种灵活性成为某种隐患如访问未定义的属性 `Cannot read property 'xxx' of undefined` 或调用非函数类型 `undefined is not a function`
TypeScript 提供强大的类型系统解决上述问题，类型系统允许在开发时进行类型检查而不必等到运行时，有助于在开发的早期发现潜在错误，确保代码中的数据类型符合预期，减少心智负担，尽管 TypeScript 限制 JavaScript 的灵活性，需要我们编写额外的类型代码，但从长远来看，TypeScript 提供的类型安全和工具确实提高开发效率和代码质量，随着项目规模的增长，强类型检查越来越具有价值，TypeScript 最终转换为 JavaScript 代码，其不影响程序的运行时性能

1. TypeScript 需编译为 JavaScript 才能在浏览器或 Node.js 环境中执行，TypeScript 编译器解析 `.ts` 或 `.tsx` 文件，将源代码转换为 AST，其为一个表示源代码结构的深层嵌套对象包括语法元素及其属性如变量声明、函数调用、类和接口等
2. TypeScript 编译器进行类型检查并验证类型的兼容性，检查类型注解和推断是否正确，若发现类型错误则输出错误并停止编译
3. TypeScript 编译器将 AST 转换为与目标 JavaScript 版本兼容的 JavaScript 代码，其应用多种转换来处理不被目标 JavaScript 版本支持的 TypeScript 特性如将 ES6 的类转换为 ES5 的函数和原型，同时移除所有 TypeScript 类型注解
4. TypeScript 编译器将转换后的 AST 再转换为可执行的 JavaScript 代码字符串并将其写入 `.js` 文件，若配置 `sourceMap` 还生成一个 sourceMap 文件，该文件映射编译后的 JavaScript 代码回原始的 TypeScript 代码，便于后续调试
