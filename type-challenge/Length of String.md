```ts
type LengthOfString<
  T extends string,
  U extends string[] = []
> = T extends `${infer L}${infer R}`
  ? LengthOfString<L, [...U, R]>
  : U["length"];
```

