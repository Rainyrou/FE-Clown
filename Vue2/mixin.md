`mixin` 允许我们抽离组件的可复用功能，通过 mixin 的方式将其添加到组件中，它可以包含任何组件选项。当组件使用`mixin` 时，所有 `mixin` 的选项将被混入该组件的选项中

1. 代码复用：`mixin` 允许我们将跨组件共享的功能和选项抽象成可复用的代码块，减少代码冗余
2. 模块化：`mixin` 允许我们将组件的不同功能模块化，提高代码的可维护性
3. 灵活性：`mixin` 提供了一种灵活的方式来扩展组件功能，而无需修改组件继承链

###### 局限性

1. 命名冲突：不同的 `mixin` 可能包含相同的方法或属性名，导致命名冲突，难以追踪问题来源
2. 隐式依赖：组件和 `mixin` 之间的依赖关系可能不够明确，使得代码的依赖关系变得难以理解，特别是在大型项目中
3. 难以调试：`mixin` 的属性和方法被直接混入到组件中，这可能会使调试变得更加困难，尤其是当多个 `mixin` 被用于同一个组件时

随着 Vue 3 引入 Composition API 的引入，mixin 逐渐被取代

`myMixin.js`

```JavaScript
export default {
    data() {
        return {
            mixinMessage: 'Hello from mixin'
        }
    },
    method: {
        showMixin() {
            alert(this.mixinMessage)
        }
    }
}
```

`MyComponent.vue`

```JavaScript
import myMixin from './myMixin.js';

export default {
    mixins: [myMixin],
    mounted() {
        this.showMixin();
    }
}
```
