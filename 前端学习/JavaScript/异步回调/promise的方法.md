
.then(onFulfilled, onRejected)`

这是 Promise 最常用的方法，用于注册当 ==Promise 状态变为 **Fulfilled** 或 **Rejected** 时的回调函数。==

| 参数            | 作用                     | 执行时机                                                                           |
| ------------- | ---------------------- | ------------------------------------------------------------------------------ |
| `onFulfilled` | 成功回调。接收成功的值作为参数。       | Promise 变为 Fulfilled (已完成) 时。                                                  |
| `onRejected`  | 失败回调（可选）。接收失败的原因作为参数。  | Promise 变为 Rejected (已拒绝) 时。                                                   |
| **返回值**       | **返回一个新的 Promise 对象。** | ==这个新的 Promise 的状态和值取决于 `onFulfilled` 或 `onRejected` 的返回值。这使得 **链式调用** 成为可能。== |

**链式调用规则：**

- 如果回调函数返回一个**非 Promise 的值**，新的 Promise 会以这个值作为成功值。
    
- 如果回调函数返回一个**新的 Promise**，==新的 Promise 的状态将与这个返回的 Promise 保持一致。==
    
- 如果回调函数**抛出异常**，==新的 Promise 会变为 Rejected 状态。==

### `.catch(onRejected)`

这是 `.then(null, onRejected)` 的语法糖，专用于处理 Promise 链中的错误

|参数|作用|执行时机|
|---|---|---|
|`onRejected`|失败回调。接收失败的原因作为参数。|链中**任何一个** Promise 变为 Rejected 时。|
|**返回值**|**返回一个新的 Promise 对象。**|如果 `onRejected` 成功处理了错误（返回一个值，而不是抛出异常），那么返回的 Promise 会变为 Fulfilled 状态。这允许你从错误中恢复，继续执行后续的 `.then()` 链。|

### `.finally(onFinally)`

用于注册一个在 Promise **无论成功或失败**（Fulfilled 或 Rejected）时都会执行的回调函数。

|参数|作用|执行时机|
|---|---|---|
|`onFinally`|回调函数。它**不接收任何参数**。|Promise 状态确定（Settled）后立即执行。|
|**返回值**|**返回一个新的 Promise 对象。**|它会继承前一个 Promise 的最终状态和值（或原因）。主要用于执行清理工作，例如关闭加载动画。|

## 二、 Promise 静态方法 (Static Methods)

这些方法是直接挂载在构造函数 ==`Promise` 上的，用于创建 Promise 或处理 Promise 的集合。==

返回一个状态为 **Fulfilled (已完成)** 的 Promise 对象。

| 参数      | 作用                                                                                |
| ------- | --------------------------------------------------------------------------------- |
| `value` | 成功的值。                                                                             |
| **返回值** | **返回一个成功的 Promise**，并将 `value` 作为其成功值。如果传入的 `value` 本身是一个 Promise，则直接返回该 Promise。 |