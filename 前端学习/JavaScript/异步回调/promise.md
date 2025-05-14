编程中经常遇到的事儿

1. “生产者代码（producing code）”会做一些事儿，并且会需要一些时间。例如，通过网络加载数据的代码。它就像一位“歌手”。

2. “消费者代码（consuming code）”想要在“生产者代码”完成工作的第一时间就能获得其工作成果。许多函数可能都需要这个结果。这些就是“粉丝”。

3. **Promise** 是将“生产者代码”和“消费者代码”连接在一起的一个特殊的 JavaScript 对象。用我们的类比来说：这就是就像是“订阅列表”。“==生产者代码”花费它所需的任意长度时间来产出所承诺的结果，而 “promise” 将在它（译注：指的是“生产者代码”，也就是下文所说的 executor）准备好时，将结果向所有订阅了的代码开放==。

这种类比并不十分准确，因为 JavaScript 的 promise 比简单的订阅列表更加复杂：它们还拥有其他的功能和局限性。但以此开始挺好的。

Promise 对象的构造器（constructor）语法如下：

```js
let promise = new Promise(function(resolve, reject) {
  // executor（生产者代码，“歌手”）
});
```

传递给 `new Promise` 的函数被称为 **executor**。==当 `new Promise` 被创建，executor 会自动运行。它包含最终应产出结果的生产者代码==。按照上面的类比：executor 就是“歌手”。

它的参数 `resolve` 和 `reject` 是由 JavaScript 自身提供的回调。我们的代码仅在 executor 的内部。

==当 executor 获得了结果，无论是早还是晚都没关系，它应该调用以下回调之一==：

- `resolve(value)` —— 如果任务成功完成并带有结果 `value`。
- `reject(error)` —— 如果出现了 error，`error` 即为 error 对象。

所以总结一下就是：executor 会自动运行并尝试执行一项工作。尝试结束后，如果成功则调用 `resolve`，如果出现 error 则调用 `reject`。

由 `new Promise` 构造器返回的 `promise` 对象具有以下内部属性：

- `state` —— 最初是 `"pending"`，然后在 `resolve` 被调用时变为 `"fulfilled"`，或者在 `reject` 被调用时变为 `"rejected"`。
- `result` —— 最初是 `undefined`，然后在 `resolve(value)` 被调用时变为 `value`，或者在 `reject(error)` 被调用时变为 `error`。

所以，executor 最终将 `promise` 移至以下状态之一：

![[Pasted image 20250514111618.png]]

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

总而言之，executor 应该执行一项工作（通常是需要花费一些时间的事儿），然后调用 `resolve` 或 `reject` 来改变对应的 promise 对象的状态。

与最初的 “pending” promise 相反，一个 resolved 或 rejected 的 promise 都会被称为 “settled”。

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