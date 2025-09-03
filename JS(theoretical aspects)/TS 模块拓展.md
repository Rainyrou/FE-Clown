1. `declare module` 扩展第三方模块的类型定义或补充其缺失的类型声明

```ts
declare module "some-library" {
  interface SomeInterface {
    newProperty: string;
    newMethod(): void;
  }

  export function newUtilityFunction(): boolean;
}
```

2. `declare global` 扩展全局接口的类型定义

```ts
declare global {
  interface Window {
    myCustomProperty: string;
    myCustomMethod(): number;
  }

  interface Array<T> {
    last: T | undefined;
  }
}
```