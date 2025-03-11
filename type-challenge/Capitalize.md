```ts
type MyCapitalize<T extends string> = T extends `${infer U}${infer Tail}`
  ? `${Uppercase<U>}${Tail}`
  : T;
```