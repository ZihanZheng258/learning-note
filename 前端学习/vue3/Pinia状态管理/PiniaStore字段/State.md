在大多数情况下，state 都是你的 store 的核心。人们通常会先定义能代表他们 APP 的 state。在 Pinia 中，state 被定义为一个返回初始状态的函数。这使得 Pinia 可以同时支持服务端和客户端。

```js
import { defineStore } from 'pinia'

const useStore = defineStore('storeId', {
  // 为了完整类型推理，推荐使用箭头函数
  state: () => {
    return {
      // 所有这些属性都将自动推断出它们的类型
      count: 0,
      name: 'Eduardo',
      isAdmin: true,
      items: [],
      hasChanged: true,
    }
  },
})
```

## 访问state
默认情况下，你可以通过 `store` 实例访问 state，直接对其进行读写。
```js
const store = useStore()

store.count++
```

注意，新的属性**如果没有在 `state()` 中被定义**，则不能被添加。它必须包含初始状态。例如：如果 `secondCount` 没有在 `state()` 中定义，我们无法执行 `store.secondCount = 2`。

## 重置 state[​](https://pinia.vuejs.org/zh/core-concepts/state.html#resetting-the-state)
使用[选项式 API](https://pinia.vuejs.org/zh/core-concepts/#option-stores) 时，你可以通过调用 store 的 `$reset()` 方法将 state 重置为初始值。

```js
const store = useStore()

store.$reset()
```

在 `$reset()` 内部，会调用 `state()` 函数来创建一个新的状态对象，并用它替换当前状态。

在 [Setup Stores](https://pinia.vuejs.org/core-concepts/#setup-stores) 中，您需要创建自己的 `$reset()` 方法：

