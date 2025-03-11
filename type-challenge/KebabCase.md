```ts
type KebabCase<T extends string> = T extends `${infer L}${infer R}`
  ? R extends Uncapitalize<R>
    ? `${Uncapitalize<L>}${KebabCase<R>}`
    : `${Uncapitalize<L>}-${KebabCase<R>}`
  : T;
```