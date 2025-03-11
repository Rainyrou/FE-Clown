```ts
type Permutation<T, U = T> = [T] extends [never]
  ? []
  : U extends U
  ? [U, ...Permutation<Exclude<T, U>>]
  : never;
```
