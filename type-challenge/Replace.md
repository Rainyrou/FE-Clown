```ts
type Replace<
  T extends string,
  From extends string,
  To extends string
> = From extends ""
  ? T
  : T extends `${infer U}${From}${infer R}`
  ? `${U}${To}${R}`
  : T;
```