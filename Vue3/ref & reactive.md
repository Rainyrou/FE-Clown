Vue 提供 `ref` 和 `reactive` 两种响应式 API 是为了覆盖不同开发场景，为开发者提供更多的选择，这体现了 Vue 的设计理念

###### ref

1. 用于包装一个原始值（如字符串、数字、布尔值等），使其变成响应式的，返回的是一个包含 `value` 属性的响应式引用对象，可通过 `.value` 属性来访问或修改内部值
2. 用于基本数据类型和引用数据类型
3. 在模板中使用时，Vue 自动解引用，不需要 `.value` 访问或修改内部值

```JavaScript
import { ref } from 'vue';

const count = ref(0);

function increment() {
  count.value++;
}
```

###### reactive

1. 用于创建一个响应式对象，直接返回传入的对象的响应式代理，不需要通过 `.value` 属性来访问或修改对象的属性
2. 只局限于引用数据类型，不能直接用于创建单一值的响应式数据
3. 在模板中使用时，无需解引用，可直接访问或修改对象的属性

```JavaScript
import { reactive } from 'vue';

const state = reactive({
  count: 0
});

function increment() {
  state.count++;
}
```

###### 底层原理

- `ref` 使用 `Proxy` 代理包装一个含 `.value` 属性的对象实现响应式
- `reactive` 同样使用 `Proxy`，但它直接在传入的对象上创建一个代理，无需额外属性来访问值

###### 为什么不只使用一个？

1. 数据类型：

- `ref` 是为了处理基本数据类型，这些类型不能直接转换为响应式数据，因为它们不是对象
- `reactive` 是为了处理引用数据类型。它允许直接在引用数据类型上创建响应式代理

2. 模板引用：

- `ref` 可用于 template refs，`reactive` 做不到

3. 解构问题：

- `ref` 可避免此问题，因为解构不会影响 `.value` 属性指向的响应式引用
- 使用 `reactive` 时，当你解构一个响应式对象时，解构出来的属性会失去响应性。这是因为解构实质上是将值复制到一个新的变量中，而这个变量不再是响应式的
