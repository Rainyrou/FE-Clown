```ts
type Flatten<T extends any[], U extends any[] = []> = T extends [
  infer L,
  ...infer R
]
  ? L extends any[]
    ? Flatten<[...L, ...R], U>
    : Flatten<[...R], [...U, L]>
  : T;
```

