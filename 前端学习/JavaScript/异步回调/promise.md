## 1. Promise（承诺）是什么？

**Promise** 是 JavaScript 中处理**异步操作**的一种机制，它代表了一个**未来才会知道结果的值**。==你可以把它想象成一个**承诺**：它承诺在未来的某个时间点，会给你一个结果（成功或失败）。==

### Promise 的核心优势

在 Promise 出现之前，我们主要通过**回调函数（Callback）**来处理异步，这经常导致“回调地狱”（Callback Hell），==代码难以阅读和维护。Promise 解决了这个问题，它提供了以下好处：==

- **链式调用 (Chaining)**：可以通过 `.then()` 方法将多个异步操作串联起来，使代码保持线性结构。
    
- **错误处理集中化**：可以使用 `.catch()` 方法集中捕获链中任何环节的错误。
    
- **清晰的状态管理**：Promise 严格遵循三种状态，避免了异步流程的混乱。

### Promise 的三种状态

一个 Promise 对象从创建到结束，只会经历以下三种状态之一：

1. **待定 (Pending)**：初始状态，既不是成功也不是失败。异步操作正在进行中。
    
2. **已完成 (Fulfilled)**：异步操作成功完成。此时，Promise 会带有一个**成功值**。
    
3. **已拒绝 (Rejected)**：异步操作失败。此时，Promise 会带有一个**拒绝原因**（通常是一个 `Error` 对象）。
    

一旦 Promise 从 **Pending** 状态变为 **Fulfilled** 或 **Rejected**，它的状态就**不可逆转地固定**了。


所以，executor 最终将 `promise` 移至以下状态之一：

![[Pasted image 20250514111618.png]]

## 2. Executor（执行器）是什么？

**Executor**（执行器函数）==是你在创建新的 Promise 实例时，必须传入的**那个函数**。它是连接同步代码和异步操作的核心桥梁。==

### Executor 的定义

当你使用 `new Promise()` 语法时，传入的就是 Executor 函数：

下面是一个 promise 构造器和一个简单的 executor 函数，该 executor 函数具有包含时间（即 `setTimeout`）的“生产者代码”：

```js
let promise = new Promise(function(resolve, reject) {
  // 当 promise 被构造完成时，自动执行此函数

  // 1 秒后发出工作已经被完成的信号，并带有结果 "done"
  setTimeout(() => resolve("done"), 1000);
});
```

通过运行上面的代码，我们可以看到两件事儿：

1. executor 被自动且立即调用（通过 `new Promise`）。
    
2. executor 接受两个参数：`resolve` 和 `reject`。这些函数由 JavaScript 引擎预先定义，因此==我们不需要创建它们。我们只需要在准备好（译注：指的是 executor 准备好）时调用其中之一即可。==
    
    经过 1 秒的“处理”后，executor 调用 `resolve("done")` 来产生结果。这将改变 `promise` 对象的状态：

这是一个成功完成任务的例子，一个“成功实现了的诺言”。

下面则是一个 executor 以 error 拒绝 promise 的示例：

```
let promise = new Promise(function(resolve, reject) {
  // 1 秒后发出工作已经被完成的信号，并带有 error
  setTimeout(() => reject(new Error("Whoops!")), 1000);
});
```

总而言之，executor 应该执行一项工作（通常是需要花费一些时间的事儿），==然后调用 `resolve` 或 `reject` 来改变对应的 promise 对象的状态。==

与最初的 “pending” promise 相反，一个 resolved 或 rejected 的 promise 都会被称为 “settled”。

### Executor 的特点和工作原理

1. **立即执行**：Executor 函数是 **同步** 执行的。当你创建 Promise 实例时，==Executor 函数会**立即启动**，而不会等待任何异步事件。==
    
2. **两个参数**：Executor 函数接收两个由 JavaScript 引擎提供的**函数**作为参数：
    
    - **`resolve` 函数**：当异步操作**成功**时，你必须调用这个函数，将 Promise 的状态从 **Pending** 转换为 **Fulfilled**，并将成功值传给它。
        
    - **`reject` 函数**：当异步操作**失败**时，你必须调用这个函数，将 Promise 的状态从 **Pending** 转换为 **Rejected**，并将失败原因传给它。
        

### 示例：Executor 的作用

## [消费者：then，catch](https://zh.javascript.info/promise-basics#xiao-fei-zhe-thencatch)

Promise 对象充当的是 executor（“生产者代码”或“歌手”）和消费函数（“粉丝”）之间的连接，后者将接收结果或 error。可以通过使用 `.then` 和 `.catch` 方法注册消费函数。

## [then](https://zh.javascript.info/promise-basics#then)

最重要最基础的一个就是 `.then`。
语法如下：

``` js
promise.then(
  function(result) { /* handle a successful result */ },
  function(error) { /* handle an error */ }
);
```

`.then` 的第一个参数是一个函数，该函数将在 promise resolved 且接收到结果后执行。

`.then` 的第二个参数也是一个函数，该函数将在 promise rejected 且接收到 error 信息后执行。

例如，以下是对成功 resolved 的 promise 做出的反应：

## catch

如果我们只对 error 感兴趣，那么我们可以使用 `null` 作为第一个参数：`.then(null, errorHandlingFunction)`。或者我们也可以使用 `.catch(errorHandlingFunction)`，其实是一样的：

```js
let promise = new Promise((resolve, reject) => {
  setTimeout(() => reject(new Error("Whoops!")), 1000);
});

// .catch(f) 与 promise.then(null, f) 一样
promise.catch(alert); // 1 秒后显示 "Error: Whoops!"
```

## [清理：finally](https://zh.javascript.info/promise-basics#qing-li-finally)

就像常规 `try {...} catch {...}` 中的 `finally` 子句一样，promise 中也有 `finally`。

调用 `.finally(f)` 类似于 `.then(f, f)`，==因为当 promise settled 时 `f` 就会执行：无论 promise 被 resolve 还是 reject。==

`finally` 的功能是设置一个处理程序在前面的操作完成后，执行清理/终结。

例如，停止加载指示器，关闭不再需要的连接等。

把它想象成派对的终结者。无论派对是好是坏，有多少朋友参加，我们都需要（或者至少应该）在它之后进行清理。

代码可能看起来像这样：

```js
new Promise((resolve, reject) => {
  /* 做一些需要时间的事，之后调用可能会 resolve 也可能会 reject */
})
  // 在 promise 为 settled 时运行，无论成功与否
  .finally(() => stop loading indicator)
  // 所以，加载指示器（loading indicator）始终会在我们继续之前停止
  .then(result => show result, err => show error)
```

请注意，`finally(f)` 并不完全是 `then(f,f)` 的别名。

它们之间有重要的区别：

1. ==`finally` 处理程序（handler）没有参数。==在 `finally` 中，我们不知道 promise 是否成功。没关系，因为我们的任务通常是执行“常规”的完成程序（finalizing procedures）。
    
    请看上面的例子：==如你所见，`finally` 处理程序没有参数，promise 的结果由下一个处理程序处理。==
    
2. `finally` 处理程序将结果或 error “传递”给下一个合适的处理程序。
    
    例如，在这结果被从 `finally` 传递给了 `then`：

```js
new Promise((resolve, reject) => {
  setTimeout(() => resolve("value"), 2000)
})
  .finally(() => alert("Promise ready")) // 先触发
  .then(result => alert(result)); // <-- .then 显示 "value"
```

正如我们所看到的，第一个 promise 返回的 `value` 通过 `finally` 被传递给了下一个 `then`。

这非常方便，因为 `finally` 并不意味着处理一个 promise 的结果。如前所述，无论结果是什么，它都是进行常规清理的地方。

3. `finally` 处理程序也不应该返回任何内容。如果它返回了，返回的值会默认被忽略。
    
    ==此规则的唯一例外是当 `finally` 处理程序抛出 error 时。此时这个 error（而不是任何之前的结果）会被转到下一个处理程序。==

- `finally` 处理程序没有得到前一个处理程序的结果（它没有参数）。而这个结果被传递给了下一个合适的处理程序。
- 如果 `finally` 处理程序返回了一些内容，那么这些内容会被忽略。
- 当 `finally` 抛出 error 时，执行将转到最近的 error 的处理程序。