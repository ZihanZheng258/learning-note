在 [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455) 规范中描述的 `WebSocket` 协议，提供了一种在浏览器和服务器之间建立持久连接来交换数据的方法。数据可以作为“数据包”在两个方向上传递，而无需中断连接也无需额外的 HTTP 请求。

对于需要连续数据交换的服务，例如网络游戏，实时交易系统等，WebSocket 尤其有用。

一旦 socket 被建立，我们就应该监听 socket 上的事件。一共有 4 个事件：

- **`open`** —— 连接已建立，
- **`message`** —— 接收到数据，
- **`error`** —— WebSocket 错误，
- **`close`** —— 连接已关闭。

……如果我们想发送一些东西，那么可以使用 `socket.send(data)`。

这是一个示例：


```javascript
let socket = new WebSocket("wss://javascript.info/article/websocket/demo/hello");

socket.onopen = function(e) {
  alert("[open] Connection established");
  alert("Sending to server");
  socket.send("My name is John");
};

socket.onmessage = function(event) {
  alert(`[message] Data received from server: ${event.data}`);
};

socket.onclose = function(event) {
  if (event.wasClean) {
    alert(`[close] Connection closed cleanly, code=${event.code} reason=${event.reason}`);
  } else {
    // 例如服务器进程被杀死或网络中断
    // 在这种情况下，event.code 通常为 1006
    alert('[close] Connection died');
  }
};

socket.onerror = function(error) {
  alert(`[error] ${error.message}`);
};
```

出于演示目的，在上面的示例中，运行着一个用 Node.js 写的小型服务器 [server.js](https://zh.javascript.info/article/websocket/demo/server.js)。它响应为 “Hello from server, John”，然后等待 5 秒，关闭连接。

所以你看到的事件顺序为：`open` → `message` → `close`。

这就是 WebSocket，我们已经可以使用 WebSocket 通信了。很简单，不是吗？

现在让我们更深入地学习它。

## [建立 WebSocket](https://zh.javascript.info/websocket#jian-li-websocket)

当 `new WebSocket(url)` 被创建后，它将立即开始连接。

在连接期间，浏览器（使用 header）问服务器：“你支持 WebSocket 吗？”如果服务器回复说“我支持”，那么通信就以 WebSocket 协议继续进行，该协议根本不是 HTTP。