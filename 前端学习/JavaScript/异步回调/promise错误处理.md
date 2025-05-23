promise 链在错误（error）处理中十分强大。当一个 promise 被 reject 时，控制权将移交至最近的 rejection 处理程序。这在实际开发中非常方便。

例如，下面代码中所 `fetch` 的 URL 是错的（没有这个网站），`.catch` 对这个 error 进行了处理：

==正如你所看到的，`.catch` 不必是立即的。它可能在一个或多个 `.then` 之后出现。==

或者，可能该网站一切正常，但响应不是有效的 JSON。==捕获所有 error 的最简单的方法是，将 `.catch` 附加到链的末尾：==

``` js
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => fetch(`https://api.github.com/users/${user.name}`))
  .then(response => response.json())
  .then(githubUser => new Promise((resolve, reject) => {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser);
    }, 3000);
  }))
  .catch(error => alert(error.message));
```

通常情况下，这样的 `.catch` 根本不会被触发。==但是如果上述任意一个 promise rejected（网络问题或者无效的 json 或其他），`.catch` 就会捕获它。==

## [隐式 try…catch](https://zh.javascript.info/promise-error-handling#yin-shi-trycatch)

promise 的执行者（executor）和 promise 的处理程序周围有一个“隐式的 `try..catch`”。如果发生异常，它就会被捕获，并被视为 rejection 进行处理。

例如，下面这段代码：

```js
new Promise((resolve, reject) => {
  throw new Error("Whoops!");
}).catch(alert); // Error: Whoops!
```

……与下面这段代码工作上完全相同：

```js
new Promise((resolve, reject) => {
  reject(new Error("Whoops!"));
}).catch(alert); // Error: Whoops!
```

在 executor 周围的“隐式 `try..catch`”自动捕获了 error，并将其变为 rejected promise。

这不仅仅发生在 executor 函数中，同样也发生在其处理程序中。==如果我们在 `.then` 处理程序中 `throw`，这意味着 promise rejected，因此控制权移交至最近的 error 处理程序。==

对于所有的 error 都会发生这种情况，而不仅仅是由 `throw` 语句导致的这些 error。例如，一个编程错误：

最后的 `.catch` 不仅会捕获显式的 rejection，还会捕获它上面的处理程序中意外出现的 error。

## [再次抛出（Rethrowing）](https://zh.javascript.info/promise-error-handling#zai-ci-pao-chu-rethrowing)

==正如我们已经注意到的，链尾端的 `.catch` 的表现有点像 `try..catch`==。我们可能有许多个 `.then` 处理程序，然后在尾端使用一个 `.catch` 处理上面的所有 error。

==在常规的 `try..catch` 中，我们可以分析 error，如果我们无法处理它==，可以将其再次抛出。对于 promise 来说，这也是可以的。

==如果我们在 `.catch` 中 `throw`，那么控制权就会被移交到下一个最近的 error 处理程序。如果我们处理该 error 并正常完成，那么它将继续到最近的成功的 `.then` 处理程序。==

在下面这个例子中，`.catch` 成功处理了 error：

```js
// 执行流：catch -> then
new Promise((resolve, reject) => {

  throw new Error("Whoops!");

}).catch(function(error) {

  alert("The error is handled, continue normally");

}).then(() => alert("Next successful handler runs"));
```

这里 `.catch` 块正常完成。所以下一个成功的 `.then` 处理程序就会被调用。

在下面的例子中，我们可以看到 `.catch` 的另一种情况。`(*)` 行的处理程序捕获了 error，但无法处理它（例如，它只知道如何处理 `URIError`），所以它将其再次抛出：

```js
// 执行流：catch -> catch
new Promise((resolve, reject) => {

  throw new Error("Whoops!");

}).catch(function(error) { // (*)

  if (error instanceof URIError) {
    // 处理它
  } else {
    alert("Can't handle such error");

    throw error; // 再次抛出此 error 或另外一个 error，执行将跳转至下一个 catch
  }

}).then(function() {
  /* 不在这里运行 */
}).catch(error => { // (**)

  alert(`The unknown error has occurred: ${error}`);
  // 不会返回任何内容 => 执行正常进行

});
```