上面提到的缓存装饰器不适用于对象方法。

例如，在下面的代码中，`worker.slow()` 在装饰后停止工作：
```JavaScript
// 我们将对 worker.slow 的结果进行缓存
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    // 可怕的 CPU 过载任务
    alert("Called with " + x);
    return x * this.someMethod(); // (*)
  }
};

// 和之前例子中的代码相同
function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    let result = func(x); // (**)
    cache.set(x, result);
    return result;
  };
}

alert( worker.slow(1) ); // 原始方法有效

worker.slow = cachingDecorator(worker.slow); // 现在对其进行缓存

alert( worker.slow(2) ); // 蛤！Error: Cannot read property 'someMethod' of undefined
```

错误发生在试图访问 `this.someMethod` 并失败了的 `(*)` 行中。你能看出来为什么吗？

原因是包装器将原始函数调用为 `(**)` 行中的 `func(x)`。并且，当这样调用时，函数将得到 `this = undefined`。

如果尝试运行下面这段代码，我们会观察到类似的问题：

```JavaScript
let func = worker.slow;
func(2);
```
因此，包装器将调用传递给原始方法，但没有上下文 `this`。因此，发生了错误。

让我们来解决这个问题。

有一个特殊的内建函数方法 [func.call(context, …args)](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Function/call)，它允许调用一个显式设置 `this` 的函数。

语法如下：
```JavaScript
func.call(context, arg1, arg2, ...)
```
它运行 `func`，提供的第一个参数作为 `this`，后面的作为参数（arguments）。

简单地说，这两个调用几乎相同：

```JavaScript
func(1, 2, 3);
func.call(obj, 1, 2, 3)
```
它们调用的都是 `func`，参数是 `1`、`2` 和 `3`。唯一的区别是 `func.call` 还会将 `this` 设置为 `obj`。

例如，在下面的代码中，我们在不同对象的上下文中调用 `sayHi`：`sayHi.call(user)` 运行 `sayHi` 并提供了 `this=user`，然后下一行设置 `this=admin`：

```JavaScript
function sayHi() {
  alert(this.name);
}

let user = { name: "John" };
let admin = { name: "Admin" };

// 使用 call 将不同的对象传递为 "this"
sayHi.call( user ); // John
sayHi.call( admin ); // Admin
```

在这里我们用带有给定上下文和 phrase 的 `call` 调用 `say`：
```JavaScript
function say(phrase) {
  alert(this.name + ': ' + phrase);
}

let user = { name: "John" };

// user 成为 this，"Hello" 成为第一个参数
say.call( user, "Hello" ); // John: Hello
```
在我们的例子中，我们可以在包装器中使用 `call` 将上下文传递给原始函数：

```JavaScript
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    alert("Called with " + x);
    return x * this.someMethod(); // (*)
  }
};

function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    let result = func.call(this, x); // 现在 "this" 被正确地传递了
    cache.set(x, result);
    return result;
  };
}

worker.slow = cachingDecorator(worker.slow); // 现在对其进行缓存

alert( worker.slow(2) ); // 工作正常
alert( worker.slow(2) ); // 工作正常，没有调用原始函数（使用的缓存）
```

现在一切都正常工作了。

为了让大家理解地更清晰一些，让我们更深入地看看 `this` 是如何被传递的：

1. 在经过装饰之后，`worker.slow` 现在是包装器 `function (x) { ... }`。
2. 因此，当 `worker.slow(2)` 执行时，包装器将 `2` 作为参数，并且 `this=worker`（它是点符号 `.` 之前的对象）。
3. 在包装器内部，假设结果尚未缓存，`func.call(this, x)` 将当前的 `this`（`=worker`）和当前的参数（`=2`）传递给原始方法。