装饰器为在编译阶段通过高阶函数修改类或方法的元数据，其通过 AST 转换实现，编译时转换为 `__decorate` 函数调用，辅助函数遍历所有装饰器并根据入参判断其调用方式：

| 类型    | 接收参数                      | 典型应用场景    |
| ----- | ------------------------- | --------- |
| 类装饰器  | `constructor: Function`   | 类扩展/元数据收集 |
| 参数装饰器 | `target, key, index`      | DI 参数注入   |
| 属性装饰器 | `target, key`             | 属性追踪      |
| 方法装饰器 | `target, key, descriptor` | 方法拦截/增强   |

装饰器表达式从上到下求值，但装饰器函数调用时执行顺序为从下到上，从右到左

* 实例成员：参数装饰器 → 属性/方法/访问器装饰器
* 静态成员：同上规则
* 构造函数：参数装饰器优先
* 类装饰器：最后执行

设置只读：

1. `readonly` 指定接口和类型别名的属性为只读

```ts
interface User {
  readonly id: string;
  name: string;
  age?: number;
}

const user: User = { id: "1", name: "Clown" };
user.id = "2"; // Cannot assign to 'id' because it is a read-only property
user.name = "Rainyrou";
```

2. `readonly` 在类中声明只读属性，在构造函数中初始化

```ts
class User {
  constructor(public readonly id: string, public name: string) {}
}

const user = new User("1", "Clown");
user.id = "2"; // Cannot assign to 'id' because it is a read-only property.
user.name = "Rainyrou";
```

3. `Readonly<T>` 指定整个对象的所有属性为只读

```ts
type User = {
  name: string;
  id: string;
  age?: number;
};

const user: Readonly<User> = { id: "1", name: "Clown" };
user.name = "Rainyrou"; // Cannot assign to 'name' because it is a read-only property.
```

内置工具类型：

`Readonly` 用于将类型 `T` 中的所有属性设置为只读

```ts
type MyReadonly<T> = { readonly [P in keyof T]: T[P] };
```

`Partial` 用于将类型 `T` 中的所有属性设置为可选

```ts
type MyPartial<T> = { [P in keyof T]?: T[P] };
```

`Require` 用于将类型 `T` 中的所有属性设置为必选

```ts
type MyRequire<T> = { [P in keyof T]: T[P] };
```

`Omit` 用于从类型 `T` 中移除指定属性 `K`，返回一个新类型，只包含不在 `K` 中的属性
`extends` -> 类型约束 + 条件判断，`in` -> 遍历类型键

```ts
type MyOmit<T, K extends keyof T> = {
  [P in keyof T as P extends K ? never : P]: T[P];
};
```

`Pick` 用于从类型 `T` 中选择指定属性 `K`，返回一个新类型，只包含在 `K` 中的属性

```ts
type MyPick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

`Record` 用于构造一个对象类型，其键源于联合类型 `K`，值为类型 `T`

```ts
type MyRecord<K extends keyof unknown, T> = {
  [P in K]: T;
};
```

`Exclude` 用于从类型 `T` 中移除所有可赋值给类型 `U` 的类型成员

```ts
type MyExclude<T, U> = T extends U ? never : T;
```

`Extract` 用于从类型 `T` 中提取所有可赋值给类型 `U` 的类型成员

```ts
type MyExact<T, U> = T extends U ? T : never;
```

`Parameters` 用于提取函数 `T` 的参数类型

```ts
type MyParameters<T extends (...args: unknown[]) => unknown> = T extends (
  ...args: infer U
) => unknown
  ? U
  : never;
```

`ReturnType` 用于提取函数 `T` 的返回类型

```ts
type MyReturnType<T extends (...args: unknown[]) => unknown> = T extends (
  ...args: unknown[]
) => infer U
  ? U
  : never;
```

`Awaited` 用于提取 Promise 或 thenable 对象 `T` 解析后的类型

```ts
type Thenable<T> = {
  then: (onfulfilled: (arg: T) => unknown) => unknown;
};

type MyAwaited<T extends Thenable<unknown> | Promise<unknown>> = T extends Promise<
  infer Inner
>
  ? Inner extends Promise<unknown>
    ? MyAwaited<Inner>
    : Inner
  : T extends Thenable<infer U>
  ? U
  : false;
```