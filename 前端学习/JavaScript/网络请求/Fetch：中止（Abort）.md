正如我们所知道的，`fetch` 返回一个 promise。例如，如果用户在我们网站上的操作表明不再需要某个执行中的 `fetch`。

为此有一个特殊的内建对象：`AbortController`。它不仅可以中止 `fetch`，还可以中止其他异步任务。

用法非常简单

## [AbortController 对象](https://zh.javascript.info/fetch-abort#abortcontroller-dui-xiang)

创建一个控制器（controller）：

```javascript
let controller = new AbortController();
```

控制器是一个极其简单的对象。

- 它具有单个方法 `abort()`，
- 和单个属性 `signal`，==我们可以在这个属性上设置事件监听器。==

当 `abort()` 被调用时：

- `controller.signal` 就会触发 `abort` 事件。
- `controller.signal.aborted` 属性变为 `true`。

通常，我们需要处理两部分：

1. 一部分是通过在 `controller.signal` 上添加一个监听器，来执行可取消操作。
2. 另一部分是触发取消：在需要的时候调用 `controller.abort()`。

这是完整的示例（目前还没有 `fetch`）：

```javascript
let controller = new AbortController();
let signal = controller.signal;

// 执行可取消操作部分
// 获取 "signal" 对象，
// 并将监听器设置为在 controller.abort() 被调用时触发
signal.addEventListener('abort', () => alert("abort!"));

// 另一部分，取消（在之后的任何时候）：
controller.abort(); // 中止！

// 事件触发，signal.aborted 变为 true
alert(signal.aborted); // true
```

正如我们所看到的，`AbortController` 只是在 `abort()` 被调用时传递 `abort` 事件的一种方式。

我们可以自己在代码中实现相同类型的事件监听，而不需要 `AbortController` 对象。

但是有价值的是，`fetch` 知道如何与 `AbortController` 对象一起工作。它们是集成在一起的。

## [与 fetch 一起使用](https://zh.javascript.info/fetch-abort#yu-fetch-yi-qi-shi-yong)

为了能够取消 `fetch`，请将 `AbortController` 的 `signal` 属性作为 `fetch` 的一个可选参数（option）进行传递：

```js
let controller = new AbortController();
fetch(url, {
  signal: controller.signal
});
```

`fetch` 方法知道如何与 `AbortController` 一起工作。==它会监听 `signal` 上的 `abort` 事件。==

现在，想要中止 `fetch`，调用 `controller.abort()` 即可：

```js
controller`.``abort``(``)``;`
```

我们完成啦：`fetch` 从 `signal` 获取了事件并中止了请求。

当一个 fetch 被中止，==它的 promise 就会以一个 error `AbortError` reject，因此我们应该对其进行处理，例如在 `try..catch` 中。==

这是完整的示例，其中 `fetch` 在 1 秒后中止：

```js
// 1 秒后中止
let controller = new AbortController();
setTimeout(() => controller.abort(), 1000);

try {
  let response = await fetch('/article/fetch-abort/demo/hang', {
    signal: controller.signal
  });
} catch(err) {
  if (err.name == 'AbortError') { // handle abort()
    alert("Aborted!");
  } else {
    throw err;
  }
}
```

## [AbortController 是可伸缩的](https://zh.javascript.info/fetch-abort#abortcontroller-shi-ke-shen-suo-de)

`AbortController` 是可伸缩的。它允许一次取消多个 fetch。

这是一个代码草稿，该代码并行 fetch 很多 `urls`，并使用单个控制器将其全部中止：

```js
let urls = [...]; // 要并行 fetch 的 url 列表

let controller = new AbortController();

// 一个 fetch promise 的数组
let fetchJobs = urls.map(url => fetch(url, {
  signal: controller.signal
}));

let results = await Promise.all(fetchJobs);

// controller.abort() 被从任何地方调用，
// 它都将中止所有 fetch
```

- `AbortController` 是一个简单的对象，当 `abort()` 方法被调用时，会在自身的 `signal` 属性上生成 `abort` 事件（并将 `signal.aborted` 设置为 `true`）。
- 
- `fetch` 与之集成：我们将 `signal` 属性作为可选参数（option）进行传递，之后 `fetch` 会监听它，因此它能够中止 `fetch`。
- 我们可以在我们的代码中使用 
- 
- `AbortController`。“调用 `abort()`” → “监听 `abort` 事件”交互简单且通用。即使没有 `fetch`，我们也可以使用它。