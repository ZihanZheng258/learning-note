## 为什么你应该使用 Pinia？[​](https://pinia.vuejs.org/zh/introduction.html#why-should-i-use-pinia)

==Pinia 是 Vue 的专属状态管理库，它允许你跨组件或页面共享状态。==如果你熟悉组合式 API 的话，你可能会认为可以通过一行简单的 `export const state = reactive({})` 来共享一个全局状态。对于单页应用来说确实可以，但如果应用在服务器端渲染，这可能会使你的应用暴露出一些[安全漏洞](https://cn.vuejs.org/guide/scaling-up/ssr#cross-request-state-pollution)。 而如果使用 Pinia，即使在小型单页应用中，你也可以获得如下功能：

- 测试工具集
- 插件：可通过插件扩展 Pinia 功能
- 为 JS 开发者提供适当的 TypeScript 支持以及**自动补全**功能。
- 支持服务端渲染
- Devtools 支持
    - 追踪 actions、mutations 的时间线
    - 在组件中展示它们所用到的 Store
    - 让调试更容易的 Time travel
- 热更新
    - 不必重载页面即可修改 Store
    - 开发时可保持当前的 State

## 创建pinia实例

```JavaScript
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const pinia = createPinia()
const app = createApp(App)

app.use(pinia)
app.mount('#app')
```

## Store 是什么？[​](https://pinia.vuejs.org/zh/getting-started.html#what-is-a-store)

Store (如 Pinia) 是一个保存状态和业务逻辑的实体，它并不与你的组件树绑定。换句话说，**它承载着全局状态**。它有点像一个永远存在的组件，每个组件都可以读取和写入它。它有**三个概念**，[state](https://pinia.vuejs.org/zh/core-concepts/state.html)、[getter](https://pinia.vuejs.org/zh/core-concepts/getters.html) 和 [action](https://pinia.vuejs.org/zh/core-concepts/actions.html)，我们可以假设这些概念相当于组件中的 `data`、 `computed` 和 `methods`。

## 应该在什么时候使用 Store?[​](https://pinia.vuejs.org/zh/getting-started.html#when-should-i-use-a-store)

一个 Store 应该包含可以在整个应用中访问的数据。这包括在许多地方使用的数据，例如显示在导航栏中的用户信息，以及需要通过页面保存的数据，例如一个非常复杂的多步骤表单。

另一方面，你应该避免在 Store 中引入那些原本可以在组件中保存的本地数据，例如，一个元素在页面中的可见性。

并非所有的应用都需要访问全局状态，但如果你的应用确实需要一个全局状态，那 Pinia 将使你的开发过程更轻松。
