```ts
type Space = " " | "\n" | "\t";
type TrimLeft<T extends string> = T extends `${Space}${infer U}`
  ? TrimLeft<U>
  : T;
```