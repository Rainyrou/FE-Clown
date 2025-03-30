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