```ts
type AppendToObject<T, U extends keyof any, R> = {
  [P in keyof T | U]: P extends keyof T ? T[P] : R;
};
```

