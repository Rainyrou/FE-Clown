```js
type MyReturnType<T extends Function> = T extends (...args: any) => infer U
  ? U
  : never;
```