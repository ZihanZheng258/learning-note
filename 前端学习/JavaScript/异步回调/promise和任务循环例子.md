## 宏任务、微任务与长时间 Executor

假设我们有以下代码：

```js
console.log('A: 同步开始'); // (1)

const longPromise = new Promise((resolve, reject) => {
    // ⬇ Executor 函数体 ⬇
    console.log('B: Executor 立即同步执行'); // (2)
    
    // 这是一个宏任务源，将回调推入宏任务队列
    setTimeout(() => {
        // 宏任务 X (5分钟后执行)
        console.log('E: 定时器到期，Promise resolve'); 
        resolve("5分钟结果");
    }, 5 * 60 * 1000); // 5分钟
    
    console.log('C: Executor 同步结束'); // (3)
    // ⬆ Executor 函数体 ⬆
});

longPromise.then(result => {
    // 微任务 1 (等待 resolve 后才推入微任务队列)
    console.log('F: Promise then 微任务'); // (6)
});

console.log('D: 同步代码结束'); // (4)
```

### 实际输出结果分析

在 **5 分钟之内**，输出顺序是：

```
A: 同步开始 
B: Executor 立即同步执行 
C: Executor 同步结束 
D: 同步代码结束
```

**5 分钟之后**，输出顺序是：

```
E: 定时器到期，Promise resolve 
F: Promise then 微任务
```

### 执行流程详解

#### 第一阶段：同步执行（瞬间完成）

1. **`console.log('A: ...')`**：立即执行，输出 **`A`**。
    
2. **`new Promise(...)` 开始执行**：
    
    - **Executor 函数被同步调用**。
        
    - **`console.log('B: ...')`**：立即执行，输出 **`B`**。
        
    - **`setTimeout` 被执行**：浏览器或 Node.js 启动一个 **5 分钟的宏任务计时器**。计时器在后台独立运行，不占用 JS 主线程时间。
        
    - **`console.log('C: ...')`**：立即执行，输出 **`C`**。
        
    - **Executor 函数同步执行完毕**。`longPromise` 状态仍为 **Pending**。
        
3. **`.then(...)` 被调用**：由于 Promise 还是 Pending 状态，`.then()` 的回调函数被注册到 `longPromise` 内部的**微任务等待列表**中。它**不会**被推入微任务队列，因为它还在等待 `resolve` 被调用。
    
4. **`console.log('D: ...')`**：继续同步执行，输出 **`D`**。
    
5. **第一轮宏任务（同步代码）执行完毕。** 微任务队列（此时为空）被清空。
    

#### 第二阶段：异步等待和宏任务执行（5分钟后）

1. **5 分钟后**：后台的 `setTimeout` 计时器到期。
    
2. **宏任务 X 入队**：`setTimeout` 的回调函数（我们称之为宏任务 X）被推入 **宏任务队列**。
    
3. **执行宏任务 X**：事件循环取出宏任务 X 执行。
    
    - **`console.log('E: ...')`**：输出 **`E`**。
        
    - **`resolve("...")` 被调用**：`longPromise` 的状态从 Pending 变为 **Fulfilled**。
        
    - JS 引擎发现 `longPromise` 已经 Fulfilled，它将之前注册的 `.then()` 回调函数（微任务 1）立即推入 **微任务队列**。
        
    - **宏任务 X 执行完毕。**
        

#### 第三阶段：清空微任务队列（紧接在宏任务 X 之后）

1. **清空微任务**：事件循环立即检查微任务队列。
    
2. **取出微任务 1**：执行 `.then()` 回调。
    
    - **`console.log('F: ...')`**：输出 **`F`**。


### 关键结论

- **Executor 总是同步的**：`B` 和 `C` 立即执行，证明了 Executor 是同步的，==它并不关心里面的 `setTimeout` 要等多久。==
    
- **`.then()` 是微任务**：`.then()` 只有在 Promise 状态确定（`resolve` 或 `reject` 被调用）之后，==才会将它的回调推入微任务队列，所以 `F` 始终在 `E` 之后。==
    
- **Promise 状态控制**：Promise 只是一个壳，真正决定它何时 Fulfilled 或 Rejected 的，是 ==Executor 内部调用的异步 API（本例中是 `setTimeout` 宏任务）。==