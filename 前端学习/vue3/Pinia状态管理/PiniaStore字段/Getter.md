Getter 完全等同于 store 的 state 的[计算值](https://cn.vuejs.org/guide/essentials/computed.html)。可以通过 `defineStore()` 中的 `getters` 属性来定义它们。**推荐**使用箭头函数，并且它将接收 `state` 作为第一个参数：

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
})
```

大多数时候，getter 仅依赖 state。不过，有时它们也可能会使用其他 getter。因此，即使在使用常规函数定义 getter 时，我们也可以通过 `this` 访问到**整个 store 实例**，**但(在 TypeScript 中)必须定义返回类型**。这是为了避免 TypeScript 的已知缺陷，**不过这不影响用箭头函数定义的 getter，也不会影响不使用 `this` 的 getter**。

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  getters: {
    // 自动推断出返回类型是一个 number
    doubleCount(state) {
      return state.count * 2
    },
    // 返回类型**必须**明确设置
    doublePlusOne(): number {
      // 整个 store 的 自动补全和类型标注 ✨
      return this.doubleCount + 1
    },
  },
})
```

与计算属性一样，你也可以组合多个 getter。通过 `this`，你可以访问到其他任何 getter。在这种情况下，**你需要为这个 getter 指定一个返回值的类型**。

```js
// 你可以在 JavaScript 中使用 JSDoc (https://jsdoc.app/tags-returns.html)
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  getters: {
    // 类型是自动推断出来的，因为我们没有使用 `this`
    doubleCount: (state) => state.count * 2,
    // 这里我们需要自己添加类型(在 JS 中使用 JSDoc)
    // 可以用 this 来引用 getter
    /**
     * 返回 count 的值乘以 2 加 1
     *
     * @returns {number}
     */
    doubleCountPlusOne() {
      // 自动补全 ✨
      return this.doubleCount + 1
    },
  },
})
```

## 向 getter 传递参数[​](https://pinia.vuejs.org/zh/core-concepts/getters.html#passing-arguments-to-getters)

_Getter_ 只是幕后的**计算**属性，所以不可以向它们传递任何参数。不过，你可以从 _getter_ 返回一个函数，该函数可以接受任意参数：

```js
export const useUserListStore = defineStore('userList', {
  getters: {
    getUserById: (state) => {
      return (userId) => state.users.find((user) => user.id === userId)
    },
  },
})
```

并在组件中使用：

```vue
<script setup>
import { useUserListStore } from './store'
const userList = useUserListStore()
const { getUserById } = storeToRefs(userList)
// 请注意，你需要使用 `getUserById.value` 来访问
// <script setup> 中的函数
</script>

<template>
  <p>User 2: {{ getUserById(2) }}</p>
</template>
```

## 访问其他 store 的 getter[​](https://pinia.vuejs.org/zh/core-concepts/getters.html#accessing-other-stores-getters)

想要使用另一个 store 的 getter 的话，那就直接在 _getter_ 内使用就好：

```js
import { useOtherStore } from './other-store'

export const useStore = defineStore('main', {
  state: () => ({
    // ...
  }),
  getters: {
    otherGetter(state) {
      const otherStore = useOtherStore()
      return state.localData + otherStore.data
    },
  },
})
```
## 使用 `setup()` 时的用法[​](https://pinia.vuejs.org/zh/core-concepts/getters.html#usage-with-setup)

作为 store 的一个属性，你可以直接访问任何 getter(与 state 属性完全一样)：

```js
<script setup>
const store = useCounterStore()
store.count = 3
store.doubleCount // 6
</script>
```