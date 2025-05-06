在深入研究核心概念之前，我们得知道 Store 是用 `defineStore()` 定义的，它的第一个参数要求是一个**独一无二的**名字：

这个**名字** ，也被用作 _id_ ，是必须传入的， Pinia 将用它来连接 store 和 devtools。为了养成习惯性的用法，将返回的函数命名为 _use..._ 是一个符合组合式函数风格的约定。

`defineStore()` 的第二个参数可接受两类值：Setup 函数或 Option 对象。

## Option Store[​](https://pinia.vuejs.org/zh/core-concepts/#option-stores)

与 Vue 的选项式 API 类似，我们也可以传入一个带有 `state`、`actions` 与 `getters` 属性的 Option 对象

```JavaScript
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0, name: 'Eduardo' }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

## Setup Store[​](https://pinia.vuejs.org/zh/core-concepts/#setup-stores)

也存在另一种定义 store 的可用语法。与 Vue 组合式 API 的 [setup 函数](https://cn.vuejs.org/api/composition-api-setup.html) 相似，我们可以传入一个函数，该函数定义了一些响应式属性和方法，并且返回一个带有我们想暴露出去的属性和方法的对象。

```js
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const doubleCount = computed(() => count.value * 2)
  function increment() {
    count.value++
  }

  return { count, doubleCount, increment }
})
```

在 _Setup Store_ 中：

- `ref()` 就是 `state` 属性
- `computed()` 就是 `getters`
- `function()` 就是 `actions`

注意，要让 pinia 正确识别 `state`，你**必须**在 setup store 中返回 **`state` 的所有属性**。这意味着，你不能在 store 中使用**私有**属性。不完整返回会影响 [SSR](https://pinia.vuejs.org/zh/cookbook/composables.html) ，开发工具和其他插件的正常运行。

Setup store 比 [Option Store](https://pinia.vuejs.org/zh/core-concepts/#option-stores) 带来了更多的灵活性，因为你可以在一个 store 内创建侦听器，并自由地使用任何[组合式函数](https://cn.vuejs.org/guide/reusability/composables.html#composables)。不过，请记住，使用组合式函数会让 SSR 变得更加复杂。

Setup store 也可以依赖于全局**提供**的属性，比如路由。任何[应用层面提供](https://vuejs.org/api/application.html#app-provide)的属性都可以在 store 中使用 `inject()` 访问，就像在组件中一样：

```JavaScript
import { inject } from 'vue'
import { useRoute } from 'vue-router'
import { defineStore } from 'pinia'

export const useSearchFilters = defineStore('search-filters', () => {
  const route = useRoute()
  // 这里假定 `app.provide('appProvided', 'value')` 已经调用过
  const appProvided = inject('appProvided')

  // ...

  return {
    // ...
  }
})
```

```JavaScript
<script setup>
import { useCounterStore } from '@/stores/counter'
// 可以在组件中的任意位置访问 `store` 变量 ✨
const store = useCounterStore()
</script>
```

你可以定义任意多的 store，但为了让使用 pinia 的益处最大化 (比如允许构建工具自动进行代码分割以及 TypeScript 推断)，**你应该在不同的文件中去定义 store**。

一旦 store 被实例化，你可以直接访问在 store 的 `state`、`getters` 和 `actions` 中定义的任何属性。我们将在后续章节继续了解这些细节，目前自动补全将帮助你使用相关属性。

请注意，`store` 是一个用 `reactive` 包装的对象，这意味着不需要在 getters 后面写 `.value`。就像 `setup` 中的 `props` 一样，**我们不能对它进行解构**：

为了从 store 中提取属性时保持其响应性，你需要使用 `storeToRefs()`。它将为每一个响应式属性创建引用。当你只使用 store 的状态而不调用任何 action 时，它会非常有用。请注意，你可以直接从 store 中解构 action，因为它们也被绑定到 store 上：

```JavaScript
<script setup>
import { storeToRefs } from 'pinia'
const store = useCounterStore()
// `name` 和 `doubleCount` 是响应式的 ref
// 同时通过插件添加的属性也会被提取为 ref
// 并且会跳过所有的 action 或非响应式 (不是 ref 或 reactive) 的属性
const { name, doubleCount } = storeToRefs(store)
// 作为 action 的 increment 可以直接解构
const { increment } = store
</script>
```


