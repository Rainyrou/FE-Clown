```vue
<template>
  <div v-if="modelValue" @click.self="closeModal">
    <slot></slot>
    <button @click="closeModal">关闭</button>
  </div>
</template>

<script setup>
const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits(["update:modelValue"]);
const closeModal = () => emit("update:modelValue", false);
</script>
```

```vue
<template>
  <div>
    <button @click="showModal = true">open</button>
    <Modal v-model="showModal">
      <p>content</p>
    </Modal>
  </div>
</template>

<script setup>
import { ref } from "vue";
import Modal from "./Child.vue";

const showModal = ref(false);
</script>
```

`v-model` 为 `prop` 属性 + 自定义事件的简写：`v-model="showModal"` === `:modelValue="showModal"`

子组件通过 `props` 接收 `showModal`，通过 `$emit` 发出 `update:modelValue` 事件