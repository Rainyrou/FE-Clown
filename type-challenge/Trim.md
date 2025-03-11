```ts
type Space = " " | "\n" | "\t";
type Trim<T extends string> = T extends
  | `${Space}${infer U}`
  | `${infer U}${Space}`
  ? Trim<U>
  : T;
```