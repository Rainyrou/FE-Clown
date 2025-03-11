```ts
type TupleToUnion<T> = T extends Array<infer U> ? U : never;
type TupleToUnion<T extends any[]> = T[number];
```