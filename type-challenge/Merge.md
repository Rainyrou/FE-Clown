```ts
type Merge<T, U> = {
  [P in keyof T | keyof U]: P extends keyof T
    ? T[P]
    : P extends keyof U
    ? U[P]
    : never;
};
```



