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
type MyRecord<K extends keyof any, T> = {
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
type MyParameters<T extends (...args: any[]) => any> = T extends (
  ...any: infer U
) => any
  ? U
  : any;
```

`ReturnType` 用于提取函数 `T` 的返回类型

```ts
type MyReturnType<T extends Function> = T extends (...args: any[]) => infer U
  ? U
  : never;
```

`Awaited` 用于提取 Promise 或 thenable 对象 `T` 解析后的类型

```ts
type Thenable<T> = {
  then: (onfulfilled: (arg: T) => unknown) => unknown;
};

type MyAwaited<T extends Thenable<any> | Promise<any>> = T extends Promise<
  infer Inner
>
  ? Inner extends Promise<any>
    ? MyAwaited<Inner>
    : Inner
  : T extends Thenable<infer U>
  ? U
  : false;
```