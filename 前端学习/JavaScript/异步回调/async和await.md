async/await 是以更舒适的方式使用 promise 的一种特殊语法，同时它也非常易于理解和使用。

## [async function](https://zh.javascript.info/async-await#asyncfunction)

让我们以 `async` 这个关键字开始。它可以被放置在一个函数前面，如下所示：

```js
async function f() {
  return 1;
}
```

==在函数前面的 “async” 这个单词表达了一个简单的事情：即这个函数总是返回一个 promise。==其他值将自动被包装在一个 resolved 的 promise 中。

例如，下面这个函数返回一个结果为 `1` 的 resolved promise，让我们测试一下：

```js
async function f() {
  return 1;
}

f().then(alert); // 1
```

……我们也可以显式地返回一个 promise，结果是一样的：

```js
async function f() {
  return Promise.resolve(1);
}

f().then(alert); // 1
```

==所以说，`async` 确保了函数返回一个 promise，也会将非 promise 的值包装进去。==很简单，对吧？但不仅仅这些。还有另外一个叫 `await` 的关键词，它只在 `async` 函数内工作，也非常酷。

## [await](https://zh.javascript.info/async-await#await)
语法如下：
```js
// 只在 async 函数内工作
let value = await promise;
```
关键字 `await` 让 JavaScript 引擎等待直到 promise 完成（settle）并返回结果。

这里的例子就是一个 1 秒后 resolve 的 promise：
```js
async function f() {

  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("done!"), 1000)
  });

  let result = await promise; // 等待，直到 promise resolve (*)

  alert(result); // "done!"
}

f();
```

==这个函数在执行的时候，“暂停”在了 `(*)` 那一行，并在 promise settle 时，拿到 `result` 作为结果继续往下执行。==所以上面这段代码在一秒后显示 “done!”。

==让我们强调一下：`await` 实际上会暂停函数的执行，直到 promise 状态变为 settled，然后以 promise 的结果继续执行。==这个行为不会耗费任何 CPU 资源，因为 JavaScript 引擎可以同时处理其他任务：执行其他脚本，处理事件等。

相比于 `promise.then`，==它只是获取 promise 的结果的一个更优雅的语法。并且也更易于读写。==

> 不能在普通函数中使用 `await`
> 如果我们尝试在非 async 函数中使用 `await`，则会报语法错误：
> 如果我们忘记在函数前面写 `async` 关键字，我们可能会得到一个这个错误。就像前面说的，`await` 只在 `async` 函数中有效。

让我们拿 [Promise 链](https://zh.javascript.info/promise-chaining) 那一章的 `showAvatar()` 例子，并将其改写成 `async/await` 的形式：

1. ==我们需要用 `await` 替换掉 `.then` 的调用。==
2. 另外，我们需要在函数前面加上 `async` 关键字，以使它们能工作。

```js
async function showAvatar() {

  // 读取我们的 JSON
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();

  // 读取 github 用户信息
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();

  // 显示头像
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = "promise-avatar-example";
  document.body.append(img);

  // 等待 3 秒
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));

  img.remove();

  return githubUser;
}

showAvatar();
```

简洁明了

## [Error 处理](https://zh.javascript.info/async-await#error-chu-li)

如果一个 promise 正常 resolve，`await promise` 返回的就是其结果。==但是如果 promise 被 reject，它将 throw 这个 error，就像在这一行有一个 `throw` 语句那样==。

```javascript
async function f() {
  await Promise.reject(new Error("Whoops!"));
}
```

```js
async function f() {
  throw new Error("Whoops!");
}
```

在真实开发中，promise 可能需要一点时间后才 reject。在这种情况下，在 `await` 抛出（throw）一个 error 之前会有一个延时。

我们可以用 `try..catch` 来捕获上面提到的那个 error，与常规的 `throw` 使用的是一样的方式：

## [总结](https://zh.javascript.info/async-await#zong-jie)

函数前面的关键字 `async` 有两个作用：

1. 让这个函数总是返回一个 promise。
2. 允许在该函数内使用 `await`。

Promise 前的关键字 `await` 使 JavaScript 引擎等待该 promise settle，然后：

1. 如果有 error，就会抛出异常 —— 就像那里调用了 `throw error` 一样。
2. 否则，就返回结果。

这两个关键字一起提供了一个很好的用来编写异步代码的框架，这种代码易于阅读也易于编写。

==有了 `async/await` 之后，我们就几乎不需要使用 `[promise.then/catch](http://promise.then/catch)`，但是不要忘了它们是基于 promise 的，因为有些时候（例如在最外层作用域）我们不得不使用这些方法。并且，当我们需要同时等待需要任务时，`Promise.all` 是很好用的。==

`await` 关键字正是 **宏任务（Macrotask）** 和 **微任务（Microtask）** 协作机制在现代 JavaScript 中最直观的体现。

`await` 关键字的作用是**暂停** `async` 函数的执行，并等待一个 Promise 解决（Settled）。这个“暂停”和“等待”的机制，==完全依赖于微任务队列来实现。==

## `await` 的工作原理：微任务的巧妙利用

当 JavaScript 引擎在 `async` 函数中遇到 `await` 关键字时，它会执行以下三个关键步骤：

### 1. 暂停执行并封装

当代码执行到 `await somePromise` 时，无论 `somePromise` 是否已经解决，引擎都会做以下动作：

- `async` 函数的执行被**暂停**。==`await` 之后的所有代码（我们称之为 **“剩余代码”**）被封装成一个**微任务**。==
    
- 这个微任务不会立即入队，它必须等待 `somePromise` 的结果。
    

### 2. 让出主线程（微任务入队）

`await` 关键字本质上是一个**让步机制**。

- **如果等待的是一个 Promise**：==`await` 会在底层调用 `.then()`，并将剩余代码作为 `.then()` 的回调函数。==这个回调函数（即我们的微任务）会被注册到 Promise 的等待列表中。
    
- **如果等待的是一个非 Promise 的值（例如 `await 5;`）**：==`await` 会将这个非 Promise 的值包装成一个立即解决的 Promise，然后将剩余代码作为微任务**立即推入微任务队列**==。
    

无论哪种情况，`await` 都会**立即将控制权交还给主线程**，允许事件循环继续执行同步代码和等待中的微任务。

### 3. 微任务恢复执行

- 当 `somePromise` 解决（Fulfilled 或 Rejected）后，==之前封装的 **微任务（剩余代码）** 就会被推入 **微任务队列**。==
    
- 根据事件循环的规则，一旦**当前宏任务**（包括所有同步代码）执行完毕，事件循环会**立即**清空微任务队列。
    
- 微任务被取出执行，`async` 函数从暂停的地方继续执行，并接收到 Promise 的结果。

## 经典示例：`await` 与宏/微任务的顺序

下面的代码演示了 `await` 是如何利用微任务“插队”到 `setTimeout`（宏任务）之前的：

``` 
async function asyncFunc() {
  console.log('B: async函数内部开始'); // (2)
  
  await null; // 相当于 await Promise.resolve(undefined);
  
  // ⬇ 这里的代码是 await 之后的“剩余代码”，被封装成微任务 1
  console.log('D: await 之后的微任务'); // (4)
}

console.log('A: 同步开始'); // (1)

setTimeout(() => console.log('E: 宏任务 setTimeout'), 0); // 宏任务 2

asyncFunc();

Promise.resolve().then(() => console.log('C: 另一个微任务')); // 微任务 2

console.log('F: 同步结束'); // (3)
```
