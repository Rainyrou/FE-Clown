>[!todo] To do 
>先看 Vue2 目录的对应部分，本文是其补充，解读官方文档和实战 demo

`<KeepAlive>`  是一个内置组件，它的功能是在多个组件间动态切换时缓存被移除的组件实例

##### 基本使用

```TypeScript
<component :is="activeComponent" />
```

默认情况下，一个组件实例在被替换掉后会被销毁。这会导致它丢失其中所有已变化的状态——当这个组件再一次被显示时，会创建一个只带有初始状态的新实例

在切换时创建新的组件实例通常是有意义的，但在这个例子中，我们的确想要组件能在被"切走"的时候保留它们的状态。要解决这个问题，我们可以用  `<KeepAlive>`  内置组件将这些动态组件包装起来：

```TypeScript
<KeepAlive>
	<component :is="activeComponent">
</KeepAlive>
```

当你将一个组件放入 `<KeepAlive>` 标签内时，这个组件不会按照常规方式进行销毁。取而代之的是，它会被放入一个缓存池中。当组件再次被激活时，它将从缓存池中恢复，而不是从头开始创建。这意味着所有在组件被缓存期间发生的状态变更都会被保存并在下次激活时保留

`<KeepAlive>`  默认会缓存内部的所有组件实例，但我们可以通过  `include`  和  `exclude` prop 来定制该行为。这两个 prop 的值都可以是一个以英文逗号分隔的字符串、一个正则表达式，或是包含这两种类型的一个数组：

```TypeScript
<KeepAlive include="a,b">
	<component :is="view" />
</KeepAlive>
<KeepAlive :include="/a|b/">
	<component :is="view" />
</KeepAlive>
<KeepAlive :include="['a', 'b']">
	<component :is="view" />
</KeepAlive>
```

它会根据组件的 `name` 选项进行匹配，因此如果一个组件要被 `<KeepAlive>` 缓存或排除，就必须显式声明一个  `name`  选项

在 3.2.34 或以上的版本中，使用  `<script setup>`  的单文件组件会自动根据文件名生成对应的  `name`  选项，无需再手动声明

我们可以通过传入  `max` prop 来限制可被缓存的最大组件实例数。`<KeepAlive>`  的行为在指定了  `max`  后类似一个  LRU 缓存：如果缓存的实例数量即将超过指定的最大数量，则最久没有被访问的缓存实例将被销毁，以便为新的实例腾出空间

```TypeScript
<KeepAlive>
	<component :is="activeComponent" />
</KeepAlive>
```

##### 缓存实例的生命周期

当一个组件实例从 DOM 上移除但因为被  `<KeepAlive>`  缓存而仍作为组件树的一部分时，它将变为不活跃状态而不是被卸载。当一个组件实例作为缓存树的一部分插入到 DOM 中时，它将被重新激活

一个持续存在的组件可以通过  `activated`  和  `deactivated`  选项来注册相应的两个状态的生命周期钩子：

最后，请注意，这两个钩子不仅仅适用于被 `<KeepAlive>` 直接包裹的组件，也适用于这个组件的任何子组件。这意味着，如果一个父组件被 `<KeepAlive>` 缓存了，它的所有子组件都会经历 `activated` 和 `deactivated` 钩子

`activated` 在组件挂载及每次从缓存中被重新插入的时候调用，`deactivated` 在从 DOM 上移除、进入缓存及组件卸载时调用

[一个 Vue 3 UI 框架 | Element Plus](https://element-plus.org/zh-CN/)

```bash
npm install element-plus --save
```

`main.ts`

```TypeScript
import { createApp } from 'vue';
import App from './App.vue';
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css';

export const app = createApp(App);

app.use(ElementPlus);
app.mount('#app');
```

`App.vue`

```typeScript
<template>
  <el-button type="primary" @click="flag = !flag">toggle</el-button>
  <keep-alive :include="['A', 'B']">
    <A v-if="flag"></A>
    <B v-else></B>
  </keep-alive>
</template>

<script setup lang='ts'>
import { ref, reactive } from 'vue';
import A from './components/A.vue';
import B from './components/B.vue';

const flag = ref<boolean>(true);
</script>

<style scoped></style>
```

`components/A.vue`

```TypeScript
<template>
    <el-card>
        <h1>I am A component</h1>
        <el-form :model="form" label-width="120px">
            <el-form-item label="Activity name">
                <el-input v-model="form.name" />
            </el-form-item>
            <el-form-item label="Activity zone">
                <el-select v-model="form.region" placeholder="please select your zone">
                    <el-option label="Zone one" value="shanghai" />
                    <el-option label="Zone two" value="beijing" />
                </el-select>
            </el-form-item>
            <el-form-item label="Activity time">
                <el-col :span="11">
                    <el-date-picker v-model="form.date1" type="date" placeholder="Pick a date" style="width: 100%" />
                </el-col>
                <el-col :span="2" class="text-center">
                    <span class="text-gray-500">-</span>
                </el-col>
                <el-col :span="11">
                    <el-time-picker v-model="form.date2" placeholder="Pick a time" style="width: 100%" />
                </el-col>
            </el-form-item>
            <el-form-item label="Instant delivery">
                <el-switch v-model="form.delivery" />
            </el-form-item>
            <el-form-item label="Activity type">
                <el-checkbox-group v-model="form.type">
                    <el-checkbox label="Online activities" name="type" />
                    <el-checkbox label="Promotion activities" name="type" />
                    <el-checkbox label="Offline activities" name="type" />
                    <el-checkbox label="Simple brand exposure" name="type" />
                </el-checkbox-group>
            </el-form-item>
            <el-form-item label="Resources">
                <el-radio-group v-model="form.resource">
                    <el-radio label="Sponsor" />
                    <el-radio label="Venue" />
                </el-radio-group>
            </el-form-item>
            <el-form-item label="Activity form">
                <el-input v-model="form.desc" type="textarea" />
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="onSubmit">Create</el-button>
                <el-button>Cancel</el-button>
            </el-form-item>
        </el-form>
    </el-card>
</template>

<script lang="ts" setup>
import { reactive, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'

// do not use same name with ref
const form = reactive({
    name: '',
    region: '',
    date1: '',
    date2: '',
    delivery: false,
    type: [],
    resource: '',
    desc: '',
})

const onSubmit = () => {
    console.log('submit!')
}

onMounted(() => {
    console.log('mounted');
})

onActivated(() => {
    console.log('keep-alive mounted');
})

onDeactivated(() => {
    console.log('keep-alive unMounted');
})

onUnmounted(() => {
    console.log('unMounted');
})
</script>
```

`components/B.vue`

```TypeScript
<template>
    <el-card>
        <h1>I am B component</h1>
        <div class="mb-2 flex items-center text-sm">
            <el-radio-group v-model="radio1" class="ml-4">
                <el-radio label="1" size="large">Option 1</el-radio>
                <el-radio label="2" size="large">Option 2</el-radio>
            </el-radio-group>
        </div>
        <div class="my-2 flex items-center text-sm">
            <el-radio-group v-model="radio2" class="ml-4">
                <el-radio label="1">Option 1</el-radio>
                <el-radio label="2">Option 2</el-radio>
            </el-radio-group>
        </div>
        <div class="my-4 flex items-center text-sm">
            <el-radio-group v-model="radio3" class="ml-4">
                <el-radio label="1" size="small">Option 1</el-radio>
                <el-radio label="2" size="small">Option 2</el-radio>
            </el-radio-group>
        </div>
        <div class="mb-2 flex items-center text-sm">
            <el-radio-group v-model="radio3" disabled class="ml-4">
                <el-radio label="1" size="small">Option 1</el-radio>
                <el-radio label="2" size="small">Option 2</el-radio>
            </el-radio-group>
        </div>
    </el-card>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const radio1 = ref('1')
const radio2 = ref('1')
const radio3 = ref('1')
</script>
```

`onMounted` 只调用了一次：

![[1696725365783.png]]


