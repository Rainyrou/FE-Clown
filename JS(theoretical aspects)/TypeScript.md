JavaScript 不要求变量有明确的类型定义，允许动态访问和修改属性，此种灵活性在小型项目中提高我们的开发效率，但随着项目规模增长及变量数量和数据类型的增加，此种灵活性成为某种隐患如访问未定义的属性 `Cannot read property 'xxx' of undefined` 或调用非函数类型 `undefined is not a function`
TypeScript 提供类型系统解决上述问题，如严格模式 + 静态类型注解 + 接口定义及类型别名 + 泛型约束 + 赋值和函数返回类型自动推断 + 类型断言 + 类型守卫 + 内置工具类型，类型系统允许在开发时进行类型检查而不必等到运行时，有助于在开发的早期发现潜在错误，确保代码中的数据类型符合预期，减少心智负担，且TypeScript 最终转换为 JavaScript 代码，不影响程序的运行时性能

1. TypeScript 需编译为 JavaScript 才能在浏览器或 Node.js 环境中执行，TypeScript 编译器解析 `.ts` 或 `.tsx` 文件，将源代码转换为 AST，其为一个表示源代码结构的深层嵌套对象包括语法元素及其属性如变量声明、函数调用、类和接口等
2. TypeScript 编译器进行类型检查并验证类型的兼容性，检查类型注解和推断是否正确，若发现类型错误则输出错误并停止编译
3. TypeScript 编译器将 AST 转换为与目标 JavaScript 版本兼容的 JavaScript 代码，其应用多种转换来处理不被目标 JavaScript 版本支持的 TypeScript 特性如将 ES6 的类转换为 ES5 的函数和原型，同时移除所有 TypeScript 类型注解
4. TypeScript 编译器将转换后的 AST 再转换为可执行的 JavaScript 代码字符串并将其写入 `.js` 文件，若配置 `sourceMap` 还生成一个 sourceMap 文件，该文件映射编译后的 JavaScript 代码回原始的 TypeScript 代码，便于后续调试
