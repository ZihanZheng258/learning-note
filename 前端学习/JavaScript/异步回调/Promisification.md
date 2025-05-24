==对于一个简单的转换来说 “Promisification” 是一个长单词。它指将一个接受回调的函数转换为一个返回 promise 的函数。==

由于许多函数和库都是基于回调的，因此，在实际开发中经常会需要进行这种转换。因为使用 promise 更加方便，所以将基于回调的函数和库 promise 化是有意义的。

例子：
```js
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));

  document.head.append(script);
}

// 用法：
// loadScript('path/script.js', (err, script) => {...})
```

我们将创建一个新的函数 `loadScriptPromise(src)`，与上面的函数作用相同（加载脚本），只是我们创建的这个函数会返回一个 promise 而不是使用回调。

```js
let loadScriptPromise = function(src) {
  return new Promise((resolve, reject) => {
    loadScript(src, (err, script) => {
      if (err) reject(err);
      else resolve(script);
    });
  });
};

// 用法：
// loadScriptPromise('path/script.js').then(...)
```

正如我们所看到的，新的函数是对原始的 `loadScript` 函数的包装。新函数调用它，并提供了自己的回调来将其转换成 promise `resolve/reject`。

现在 `loadScriptPromise` 非常适用于基于 promise 的代码了。如果我们相比于回调函数，更喜欢 promise（稍后我们将看到更多喜欢 promise 的原因），那么我们将改用它。

在实际开发中，我们可能需要 promise 化很多函数，所以使用一个 helper（辅助函数）很有意义。

我们将其称为 `promisify(f)`：它接受一个需要被 promise 化的函数 `f`，并返回一个包装（wrapper）函数。

```js
function promisify(f) {
  return function (...args) { // 返回一个包装函数（wrapper-function） (*)
    return new Promise((resolve, reject) => {
      function callback(err, result) { // 我们对 f 的自定义的回调 (**)
        if (err) {
          reject(err);
        } else {
          resolve(result);
        }
      }

      args.push(callback); // 将我们的自定义的回调附加到 f 参数（arguments）的末尾

      f.call(this, ...args); // 调用原始的函数
    });
  };
}

// 用法：
let loadScriptPromise = promisify(loadScript);
loadScriptPromise(...).then(...);
```

代码看起来可能有些复杂，但其本质与我们在上面写的那个是一样的，就是将 `loadScript` 函数 promise 化。

调用 `promisify(f)` 会返回一个 `f` `(*)` 的包装器。该包装器返回一个 promise，并将调用转发给原始的 `f`，并在我们自定义的回调 `(**)` 中跟踪结果。

在这里，`promisify` 假设原始函数期望一个带有两个参数 `(err, result)` 的回调。这就是我们最常遇到的形式。那么我们自定义的回调的格式是完全正确的，在这种情况下 `promisify` 也可以完美地运行。

但是如果原始的 `f` 期望一个带有更多参数的回调 `callback(err, res1, res2, ...)`，该怎么办呢？

我们可以继续改进我们的辅助函数。让我们写一个更高阶版本的 `promisify`。

- 当它被以 `promisify(f)` 的形式调用时，它应该以与上面那个版本的实现的工作方式类似。
- 当它被以 `promisify(f, true)` 的形式调用时，它应该返回以回调函数数组为结果 resolve 的 promise。这就是具有很多个参数的回调的结果。

```js
/ promisify(f, true) 来获取结果数组
function promisify(f, manyArgs = false) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      function callback(err, ...results) { // 我们自定义的 f 的回调
        if (err) {
          reject(err);
        } else {
          // 如果 manyArgs 被指定，则使用所有回调的结果 resolve
          resolve(manyArgs ? results : results[0]);
        }
      }

      args.push(callback);

      f.call(this, ...args);
    });
  };
}

// 用法：
f = promisify(f, true);
f(...).then(arrayOfResults => ..., err => ...);
```