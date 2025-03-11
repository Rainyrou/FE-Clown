```ts
type LookUp<T, U> = T extends { type: U } ? T : never;
```