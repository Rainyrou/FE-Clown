```ts
type Chainable<T = {}> = {
  option: <K extends string, V>(
    key: K extends keyof T ? (V extends T[K] ? never : K) : K,
    value: V
  ) => Chainable<Omit<T, K> & Record<K, V>>;
  get: () => T;
};
```