CSS Modules 基于构建工具在处理 CSS 文件时，自动为每个类名生成唯一标识，用于解决 CSS 全局性导致的命名冲突、重复类名等问题，它让 CSS 类名局部作用于当前组件，而不是全局作用域

1. 在默认情况下，CSS Modules 将所有类名看作局部作用域，这些类名只在当前 CSS 文件中有效
2. 在构建过程中，CSS Modules 自动为每个类名生成唯一标识，它由原始类名、文件名和一个哈希值组成，以确保全局唯一性
3. 处理后的 CSS 文件导出一个包含原始类名与生成唯一标识映射的对象，这使在 JavaScript 中引用这些类名时，使用映射后的唯一标识，而非原始类名
4. 在 JavaScript 文件中通过特定的导入语句引入 CSS Module，再通过映射对象动态引用 CSS 类名，确保样式的模块化
