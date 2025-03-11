```ts
type Thenable<T> = {
  then: (onfulfilled: (arg: T) => unknown) => unknown;
};

type MyAwaited<T extends Thenable<any> | Promise<any>> = T extends Promise<
  infer Inner
>
  ? Inner extends Promise<any>
    ? MyAwaited<Inner>
    : Inner
  : T extends Thenable<infer U>
  ? U
  : false;
```