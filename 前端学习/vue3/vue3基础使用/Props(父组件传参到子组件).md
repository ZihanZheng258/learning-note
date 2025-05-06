子组件可以通过 **props** 从父组件接受动态数据。首先，需要声明它所接受的 props：
```vue
<!-- ChildComp.vue -->
<script setup>
const props = defineProps({
  msg: String
})
</script>
```

注意 `defineProps()` 是一个编译时宏，并不需要导入。一旦声明，`msg` prop 就可以在子组件的模板中使用。它也可以通过 `defineProps()` 所返回的对象在 JavaScript 中访问。

父组件可以像声明 HTML attributes 一样传递 props。若要传递动态值，也可以使用 `v-bind` 语法：

```html
<ChildComp :msg="greeting" />
```
现在在编辑器中自己尝试一下吧。

## child
```vue
<script setup>
const props = defineProps({
  msg: String
})
</script>

<template>
  <h2>{{ msg || 'No props passed yet' }}</h2>
</template>
```
## parent

```vue
<script setup>
import { ref } from 'vue'
import ChildComp from './ChildComp.vue'

const greeting = ref('Hello from parent')
</script>

<template>
  <ChildComp :msg="greeting" />
</template>
```
