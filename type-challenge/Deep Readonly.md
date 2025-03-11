```ts
type DeepReadonly<T> = keyof T extends never
  ? T
  : { readonly [P in keyof T]: DeepReadonly<T[P]> };
```