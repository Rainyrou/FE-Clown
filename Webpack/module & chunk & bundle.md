Module：模块是代码复用和组织的基本单位，将一个大型程序分割成独立片段即模块，每个模块执行一个独立的任务，且可被其他模块按需引用。Webpack 忽略具体资源类型的差异，将所有文件统一看作 module
Chunk：Webpack 在内部处理模块并决定如何合并它们生成最终输出文件时的中间表示。一个 chunk 由多个 module 组成，可看作是模块的集合。它的出现是为了优化输出结果，Webpack 支持代码分割，将代码拆分成多个 chunks，用户只在需要时才加载对应的 chunk，此外 Webpack 内置的 `SplitChunksPlugin` 插件用于自动优化代码输出，根据配置将第三方库 vendors、公共模块等提取到单独的 chunk 中
Bundle：Webpack 打包过程的最终输出，每个 bundle 均为一个包含转换和合并后的资源的文件，它是浏览器下载和使用的实际文件。在构建完成后，我们会得到一或多个 bundle 文件，取决于 Webpack 配置

