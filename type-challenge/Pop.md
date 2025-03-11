```ts
type Pop<T extends any[]> = T extends [...infer U, infer _] ? U : never;
```