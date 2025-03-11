```ts
type First<T extends any[]> = T extends [] ? never : T[0];
type First<T extends any[]> = T[number] extends never ? never : T[0];
```