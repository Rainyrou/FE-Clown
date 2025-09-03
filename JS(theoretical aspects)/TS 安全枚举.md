1. 字面量类型联合

```ts
type SafeDirection = "north" | "south" | "west" | "east";

const move = (direction: SafeDirection) => console.log(`Moving ${direction}`);
move("north");
move("south");
move("up"); // Argument of type '"up"' is not assignable to parameter of type 'SafeDirection'.
```

2. 对象字面量 + as const

```ts
const Direction = {
  North: "north",
  East: "east",
  South: "south",
  West: "west",
} as const;

type SafeDirection = (typeof Direction)[keyof typeof Direction];

const move = (direction: SafeDirection) => console.log(`Moving ${direction}`);
move(Direction.South);
move("south");
move("up"); // Argument of type '"up"' is not assignable to parameter of type 'SafeDirection'
```

对象类型推导 → 键名联合 → 值联合

* `as const` 用于强制将对象属性推断为字面量类型而非更宽泛的 `string` 类型且属性变为只读
* `keyof` 用于提取类型的所有键名，返回由它们组成的联合类型
* Type[Key]：索引访问类型