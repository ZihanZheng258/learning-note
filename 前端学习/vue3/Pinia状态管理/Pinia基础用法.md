下面就是 pinia API 的基本用法 (为继续阅读本简介请确保你已阅读过了[开始](https://pinia.vuejs.org/zh/getting-started.html)章节)。你可以先创建一个 Store：

```JavaScript
// stores/counter.js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => {
    return { count: 0 }
  },
  // 也可以这样定义
  // state: () => ({ count: 0 })
  actions: {
    increment() {
      this.count++
    },
  },
})
```

然后你就可以在一个组件中_使用_该 store 了：

```vue
<script setup>
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()

counter.count++
// 自动补全！ ✨
counter.$patch({ count: counter.count + 1 })
// 或使用 action 代替
counter.increment()
</script>

<template>
  <!-- 直接从 store 中访问 state -->
  <div>Current Count: {{ counter.count }}</div>
</template>
```
为实现更多高级用法，你甚至可以使用一个函数 (与组件 `setup()` 类似) 来定义一个 Store

```js
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  function increment() {
    count.value++
  }

  return { count, increment }
})
```

