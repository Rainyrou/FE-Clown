`Array.prototype.sort` 在 ECMAScript 标准中无明确指定使用某一排序算法，因此不同 JavaScript 引擎有不同实现，对于包含多种数据类型元素的数组，`sort` 首先将元素统一转换为字符串，再进行比较（除非提供比较函数），若提供比较函数，`sort` 使用该函数的返回值来决定元素间的排序关系

1. V8 引擎（Chrome, Node.js）：插入排序 -> 10个以内元素数组，快排 + Timsort -> 大于10个元素数组
2. SpiderMonkey（Firefox）& JavaScriptCore（Safari）：归并排序

Timsort 结合插入和归并排序的优点，其时间复杂度为 O(n log n)，将输入数组拆分为多个小块，大小为 32 或 64，使用插入排序对单个小块进行排序，最后使用优化过的归并排序将所有小块拼接成最终的数组

