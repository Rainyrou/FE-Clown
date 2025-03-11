```ts
type ReplaceAll<
  T extends string,
  From extends string,
  To extends string
> = From extends ""
  ? T
  : T extends `${infer U}${From}${infer R}`
  ? `${U}${To}${ReplaceAll<R, From, To>}`
  : T;
```