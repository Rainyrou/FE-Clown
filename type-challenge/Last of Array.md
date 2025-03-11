```ts
type LastOfArray<T extends any[]> = [any, ...T][T["length"]];
```