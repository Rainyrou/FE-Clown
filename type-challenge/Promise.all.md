```ts
declare function PromiseAll<T extends any[]>(
  value: readonly [...T]
): Promise<{ [P in keyof T]: T[P] extends Promise<infer U> ? U : T[P] }>;
```