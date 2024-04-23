Vue2 响应式的一大缺陷是增删对象属性后，该属性不会自动变成响应式，`$set` 用于在响应式对象上动态添加新属性，Vue 在底层上检查对象是否为响应式，若是，直接赋值返回，接着检查对象属性是否为响应式，若是，直接赋值返回，若属性不存在，通过 `Object.defineProperty` 实现响应式原理

```JavaScript
this.$set(object, propertyName, value);
```

依次为增加的属性对象、属性名和属性值
