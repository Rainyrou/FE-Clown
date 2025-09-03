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