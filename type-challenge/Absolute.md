```ts
type Absolute<T extends string | number | bigint> = `${T}` extends `-${infer U}`
  ? `${U}`
  : `${T}`;
```

