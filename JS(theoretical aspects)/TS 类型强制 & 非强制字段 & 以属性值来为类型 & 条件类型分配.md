###### 类型强制 & 非强制字段

| 场景         | 方案                           | 特点                 |
| ---------- | ---------------------------- | ------------------ |
| 定义强制/非强制字段 | 通过可选修饰符 `?`                  | 静态类型结构             |
| 动态组合字段     | `Partial<T>` + `Required<T>` | 复用/拓展已有类型          |
| 选择/移除组合字段  | `Pick<T>` + `Omit<T>`        | 重构复杂类型             |
| 条件性强制字段    | 条件类型 `extends`               | 根据泛型参数动态调整强制/非强制字段 |

动态组合字段：

```ts
type UserCore = {
  name: string;
  id: string;
};

type UserOption = Partial<{
  age: number;
}>;

type User = UserCore & UserOption;
```

选择/移除组合字段：

```ts
interface FullUser {
  name: string;
  id: string;
  age: number;
}

type UserCore = Pick<FullUser, "id" | "name">;
type UserOption = Partial<Pick<FullUser, "age">>;
type User = UserCore & UserOption;
```

条件性强制字段：

```ts
type User<T extends boolean> = {
  id: string;
  name: string;
} & (T extends true ? { age: number } : { age?: number });

const user1: User<true> = {
  id: "1",
  name: "Clown",
  age: 21,
};

const user2: User<false> = {
  id: "2",
  name: "Rainyrou",
};
```

###### 以属性值来为类型

`as const` + `typeof`

```ts
const config = {
  status: "success",
  code: 200,
} as const;

type StatusType = typeof config.status; // "success"
```

```ts
const directions = {
  up: "UP",
  down: "DOWN",
  left: "LEFT",
  right: "RIGHT",
} as const;

type Direction = (typeof directions)[keyof typeof directions];
```

###### 条件类型分配

1. 定义条件类型 `T extends U ? X : Y`，其中 `T` 为泛型类型参数且该参数为联合类型（裸类型参数 -> 未被包裹于其他类型
2. 当 `T` 传入联合类型时，TypeScript 自动对各个成员应用条件判断并将合并结果为新联合类型