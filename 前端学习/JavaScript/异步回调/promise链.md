我们回顾一下 [简介：回调](https://zh.javascript.info/callbacks) 一章中提到的问题：==我们有一系列的异步任务要一个接一个地执行 —— 例如，加载脚本。我们如何写出更好的代码呢？==

```js
new Promise(function(resolve, reject) {

  setTimeout(() => resolve(1), 1000); // (*)

}).then(function(result) { // (**)

  alert(result); // 1
  return result * 2;

}).then(function(result) { // (***)

  alert(result); // 2
  return result * 2;

}).then(function(result) {

  alert(result); // 4
  return result * 2;

});
```

它的想法是通过 `.then` 处理程序（handler）链进行传递 result。

运行流程如下：

1. 初始 promise 在 1 秒后 resolve `(*)`，
2. 然后 `.then` 处理程序被调用 `(**)`，它又创建了一个新的 promise（以 `2` 作为值 resolve）。
3. 下一个 `then` `(***)` 得到了前一个 `then` 的值，对该值进行处理（*2）并将其传递给下一个处理程序。
4. ……依此类推。

这样之所以是可行的，==是因为每个对 `.then` 的调用都会返回了一个新的 promise，因此我们可以在其之上调用下一个 `.then`==。

当处理程序返回一个值时，它将成为该 promise 的 result，==所以将使用它调用下一个 `.then`==。

**新手常犯的一个经典错误：从技术上讲，==我们也可以将多个 `.then` 添加到一个 promise 上。但这并不是 promise 链（chaining）。**==

## [返回 promise](https://zh.javascript.info/promise-chaining#fan-hui-promise)

`.then(handler)` 中所使用的处理程序（handler）可以创建并返回一个 promise。

在这种情况下，其他的处理程序将等待它 settled 后再获得其结果。

例如：
``` js
new Promise(function(resolve, reject) {

  setTimeout(() => resolve(1), 1000);

}).then(function(result) {

  alert(result); // 1

  return new Promise((resolve, reject) => { // (*)
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) { // (**)

  alert(result); // 2

  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) {

  alert(result); // 4

});
```

我们将整个执行过程分为几个关键的时间点。请注意，`alert()` 是一个阻塞浏览器主线程的操作，但在这里我们主要关注任务调度的顺序和时间点。

### 阶段 1：同步启动和第一个宏任务入队 (时间点 t0​)

1. **宏任务 1 开始**：整个代码块（即 `<script>`）作为第一个宏任务开始执行。
    
2. **Promise Executor 同步执行**：
    
    - `new Promise(...)` 的 **Executor 函数** 被立即同步调用。
        
    - **`setTimeout(() => resolve(1), 1000)`**：这是一个宏任务源。浏览器启动一个 **1000 毫秒 (1 秒)** 的定时器。定时器到期后，`resolve(1)` 的逻辑（我们称之为 **宏任务 A**）会被推入 **宏任务队列**。
        
3. **`.then()` 注册**：第一个 `.then()` 回调被注册到 Promise 实例的内部等待列表。
    
4. **宏任务 1 结束**：同步代码执行完毕。事件循环检查微任务队列（此时为空），然后等待。
    

- **当前状态**：主线程空闲，等待 1 秒。
    
- **宏任务队列**：[等待 1 秒后入队的 **宏任务 A**]
    

---

### 阶段 2：第一个 Promise 完成和第一个 `.then()` 执行 (时间点 t0​+1000ms)

1. **宏任务 A 启动**：1 秒后，计时器到期，**宏任务 A** 被取出执行。
    
    - **`resolve(1)` 被调用**：第一个 Promise 的状态变为 **Fulfilled**，值为 `1`。
        
    - **微任务入队**：Promise 状态改变，它的第一个 `.then()` 回调（我们称之为 **微任务 1**）立即被推入 **微任务队列**。
        
2. **宏任务 A 结束**：主线程空闲，事件循环立即检查微任务队列。
    
3. **清空微任务队列**：执行 **微任务 1**。
    
    - **`alert(1)`**：执行，输出 `1`。
        
    - **`return new Promise(...)`**：返回一个新的 Promise。这个 Promise 的 Executor 同步执行。
        
    - **`setTimeout(() => resolve(result * 2), 1000)`**：启动第二个 **1000 毫秒 (1 秒)** 的定时器。定时器到期后，`resolve(2)` 的逻辑（我们称之为 **宏任务 B**）会被推入 **宏任务队列**。
        
    - 微任务队列清空。
        

- **当前状态**：主线程空闲，等待 1 秒。
    
- **宏任务队列**：[等待 1 秒后入队的 **宏任务 B**]
    

---

### 阶段 3：第二个 Promise 完成和第二个 `.then()` 执行 (时间点 t0​+2000ms)

1. **宏任务 B 启动**：再过 1 秒（总共 2 秒），第二个计时器到期，**宏任务 B** 被取出执行。
    
    - ==**`resolve(2)` 被调用**：第二个 Promise 的状态变为 **Fulfilled**，值为 `2`。==
        
    - **微任务入队**：它的 `.then()` 回调（位于 `(**)` 处，我们称之为 **微任务 2**）立即被推入 **微任务队列**。
        
2. **宏任务 B 结束**：主线程空闲，事件循环立即检查微任务队列。
    
3. **清空微任务队列**：执行 **微任务 2**。
    
    - **`alert(2)`**：执行，输出 `2`。
        
    - **`return new Promise(...)`**：返回一个新的 Promise。这个 Promise 的 Executor 同步执行。
        
    - **`setTimeout(() => resolve(result * 2), 1000)`**：启动第三个 **1000 毫秒 (1 秒)** 的定时器。定时器到期后，`resolve(4)` 的逻辑（我们称之为 **宏任务 C**）会被推入 **宏任务队列**。
        
    - 微任务队列清空。
        

- **当前状态**：主线程空闲，等待 1 秒。
    
- **宏任务队列**：[等待 1 秒后入队的 **宏任务 C**]
    

---

### 阶段 4：第三个 Promise 完成和最终 `.then()` 执行 (时间点 t0​+3000ms)

1. **宏任务 C 启动**：再过 1 秒（总共 3 秒），第三个计时器到期，**宏任务 C** 被取出执行。
    
    - **`resolve(4)` 被调用**：第三个 Promise 的状态变为 **Fulfilled**，值为 `4`。
        
    - **微任务入队**：它的最终 `.then()` 回调（我们称之为 **微任务 3**）立即被推入 **微任务队列**。
        
2. **宏任务 C 结束**：主线程空闲，事件循环立即检查微任务队列。
    
3. **清空微任务队列**：执行 **微任务 3**。
    
    - **`alert(4)`**：执行，输出 `4`。
        
    - 微任务队列清空。
        

- **最终状态**：所有任务执行完毕。