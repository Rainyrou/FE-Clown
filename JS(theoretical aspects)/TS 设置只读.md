1. `readonly` 指定接口和类型别名的属性为只读

```ts
interface User {
  readonly id: string;
  name: string;
  age?: number;
}

const user: User = { id: "1", name: "Clown" };
user.id = "2"; // Cannot assign to 'id' because it is a read-only property
user.name = "Rainyrou";
```

2. `readonly` 在类中声明只读属性，在构造函数中初始化

```ts
class User {
  constructor(public readonly id: string, public name: string) {}
}

const user = new User("1", "Clown");
user.id = "2"; // Cannot assign to 'id' because it is a read-only property.
user.name = "Rainyrou";
```

3. `Readonly<T>` 指定整个对象的所有属性为只读

```ts
type User = {
  name: string;
  id: string;
  age?: number;
};

const user: Readonly<User> = { id: "1", name: "Clown" };
user.name = "Rainyrou"; // Cannot assign to 'name' because it is a read-only property.
```

