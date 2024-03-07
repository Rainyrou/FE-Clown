Vue3 的 hook 实际上是通过 Composition API 中的 Lifecycle Hook Functions 和 Composition Functions 实现的，这些 API 提供了一种在组件外部封装和复用逻辑的方法，同时允许开发者在合适的时机钩入组件的生命周期

Vue3 允许在 `setup` 函数内部直接访问组件的生命周期钩子，而组合函数通过在函数中使用响应式状态、生命周期钩子和其他 Vue 特性，再将其导出给使用它的组件，与 React Hooks 的理念非常类似

```JavaScript
import { ref, onMounted, onUnmounted } from 'vue';

export function useWindowSize() {
    const width = ref(window.innerWidth);
    const height = ref(window.innerHeight);

    const update = () => {
        width.value = window.innerWidth;
        height.value = window.innerHeight;
    }

    onMounted(() => window.addEventListener('resize', update));
    onUnmounted(() => window.addEventListener('resize', update));

    return { width, height };
}
```

在组件中使用它：

```JavaScript
import { useWindowSize } from './useWindowSize';

export default {
  setup() {
    const { width, height } = useWindowSize();
    return { width, height };
  }
};
```

