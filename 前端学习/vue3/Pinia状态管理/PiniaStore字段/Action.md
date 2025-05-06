Action 相当于组件中的 [method](https://cn.vuejs.org/api/options-state.html#methods)。它们可以通过 `defineStore()` 中的 `actions` 属性来定义，**并且它们也是定义业务逻辑的完美选择。**

```js
export const useCounterStore = defineStore('main', {
  state: () => ({
    count: 0,
  }),
  actions: {
    increment() {
      this.count++
    },
    randomizeCounter() {
      this.count = Math.round(100 * Math.random())
    },
  },
})
```

类似 [getter](https://pinia.vuejs.org/zh/core-concepts/getters.html)，action 也可通过 `this` 访问**整个 store 实例**，并支持**完整的类型标注(以及自动补全✨)**。**不同的是，`action` 可以是异步的**，你可以在它们里面 `await` 调用任何 API，以及其他 action！下面是一个使用 [Mande](https://github.com/posva/mande) 的例子。请注意，你使用什么库并不重要，只要你得到的是一个`Promise`。你甚至可以 (在浏览器中) 使用原生 `fetch` 函数：

```js
import { mande } from 'mande'

const api = mande('/api/users')

export const useUsers = defineStore('users', {
  state: () => ({
    userData: null,
    // ...
  }),

  actions: {
    async registerUser(login, password) {
      try {
        this.userData = await api.post({ login, password })
        showTooltip(`Welcome back ${this.userData.name}!`)
      } catch (error) {
        showTooltip(error)
        // 让表单组件显示错误
        return error
      }
    },
  },
})
```

你也完全可以自由地设置任何你想要的参数以及返回任何结果。当调用 action 时，一切类型也都是可以被自动推断出来的。

Action 可以像函数或者通常意义上的方法一样被调用：

```js
<script setup>
const store = useCounterStore()
// 将 action 作为 store 的方法进行调用
store.randomizeCounter()
</script>
<template>
  <!-- 即使在模板中也可以 -->
  <button @click="store.randomizeCounter()">Randomize</button>
</template>
```

## 访问其他 store 的 action[​](https://pinia.vuejs.org/zh/core-concepts/actions.html#accessing-other-stores-actions)

想要使用另一个 store 的话，那你直接在 _action_ 中调用就好了：
```js
import { useAuthStore } from './auth-store'

export const useSettingsStore = defineStore('settings', {
  state: () => ({
    preferences: null,
    // ...
  }),
  actions: {
    async fetchUserPreferences() {
      const auth = useAuthStore()
      if (auth.isAuthenticated) {
        this.preferences = await fetchPreferences()
      } else {
        throw new Error('User must be authenticated')
      }
    },
  },
})
```

## 使用选项式 API 的用法

```js
// 示例文件路径：
// ./src/stores/counter.js

import { defineStore } from 'pinia'

const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  actions: {
    increment() {
      this.count++
    },
  },
})
```

## 订阅 action[​](https://pinia.vuejs.org/zh/core-concepts/actions.html#subscribing-to-actions)

你可以通过 `store.$onAction()` 来监听 action 和它们的结果。传递给它的回调函数会在 action 本身之前执行。`after` 表示在 promise 解决之后，允许你在 action 解决后执行一个回调函数。同样地，`onError` 允许你在 action 抛出错误或 reject 时执行一个回调函数。这些函数对于追踪运行时错误非常有用，类似于[Vue docs 中的这个提示](https://cn.vuejs.org/guide/best-practices/production-deployment#tracking-runtime-errors)。

```js
const unsubscribe = someStore.$onAction(
  ({
    name, // action 名称
    store, // store 实例，类似 `someStore`
    args, // 传递给 action 的参数数组
    after, // 在 action 返回或解决后的钩子
    onError, // action 抛出或拒绝的钩子
  }) => {
    // 为这个特定的 action 调用提供一个共享变量
    const startTime = Date.now()
    // 这将在执行 "store "的 action 之前触发。
    console.log(`Start "${name}" with params [${args.join(', ')}].`)

    // 这将在 action 成功并完全运行后触发。
    // 它等待着任何返回的 promise
    after((result) => {
      console.log(
        `Finished "${name}" after ${
          Date.now() - startTime
        }ms.\nResult: ${result}.`
      )
    })

    // 如果 action 抛出或返回一个拒绝的 promise，这将触发
    onError((error) => {
      console.warn(
        `Failed "${name}" after ${Date.now() - startTime}ms.\nError: ${error}.`
      )
    })
  }
)

// 手动删除监听器
unsubscribe()
```