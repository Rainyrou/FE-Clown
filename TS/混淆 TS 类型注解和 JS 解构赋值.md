```TypeScript
export const lazyLoadComponentIfVisible = ({
  componentLoader: AsyncComponentLoader;
  loadingComponent: Component;
  errorComponent?: Component;
  delay?: number;
  timeout?: number;
}) => {};
```

在函数参数中直接声明类型是不合法的。在 TypeScript 中，类型注解应在函数参数外部声明参数的类型，整个类型声明应被包裹在一个类型或接口声明中，而不是直接放在参数括号中

正确的写法：定义一个接口或类型表示函数参数的结构，在函数参数中使用该接口或类型

```TypeScript
interface LazyLoadOptions {
  componentLoader: AsyncComponentLoader;
  loadingComponent: Component;
  errorComponent?: Component;
  delay?: number;
  timeout?: number;
}

export const lazyLoadComponentIfVisible = ({
  componentLoader,
  loadingComponent,
  errorComponent,
  delay,
  timeout
}: LazyLoadOptions) => {};
```

