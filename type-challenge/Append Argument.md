```ts
type AppendArgument<Fn, T> = Fn extends (...args: infer U) => infer R
  ? (...args: [...U, T]) => R
  : never;
```