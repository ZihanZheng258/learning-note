**`fetch()`** 是 JavaScript 中现代、基于 **Promise** 的 **API**，用于在网络上（或本地）发起 **HTTP 请求**来获取资源。==它旨在替代老旧的 `XMLHttpRequest` (XHR) 对象，让网络请求更加简洁和强大。==

`fetch()` 默认发送 **GET** 请求，用于从服务器获取数据。它只需要一个参数：资源的 URL。

### **使用 Promise 链**

`fetch()` 函数返回一个 **Promise**，它会解析为一个 `Response` 对象。

```js
fetch('https://api.example.com/data')
  // 1. 接收 Response 对象
  .then(response => {
    // 检查响应状态（非网络错误时 404/500 也算成功，需要手动检查）
    if (!response.ok) {
      throw new Error(`HTTP 错误！状态码: ${response.status}`);
    }
    // 2. 将响应体解析为 JSON 格式（这也是一个 Promise）
    return response.json(); 
  })
  // 3. 接收最终的 JSON 数据
  .then(data => {
    console.log('获取到的数据:', data);
  })
  // 4. 捕获任何错误（网络错误或 Promise 链中的错误）
  .catch(error => {
    console.error('获取数据失败:', error);
  });
```

### **使用 async/await (推荐)**

在 `async` 函数中使用 `await` 关键字可以让代码看起来像同步代码一样直观，是推荐的写法。

```js
async function fetchData() {
  const url = 'https://api.example.com/data';
  
  try {
    // 1. 等待 fetch 完成并获取 Response 对象
    const response = await fetch(url);

    // 2. 检查 HTTP 状态码是否成功 (200-299)
    if (!response.ok) {
      throw new Error(`HTTP 错误！状态码: ${response.status}`);
    }

    // 3. 等待将响应体解析为 JSON 数据
    const data = await response.json();
    console.log('获取到的数据:', data);

  } catch (error) {
    console.error('获取数据失败:', error);
  }
}

fetchData();
```

## 2. 发送 POST 请求 (提交数据)

如果你需要向服务器发送数据（例如提交表单或创建新资源），你需要提供一个包含请求配置的**第二个参数（一个配置对象）**。

### **POST 请求配置示例 (发送 JSON)**

发送 JSON 数据是最常见的 POST 场景。你需要设置 `method`、`headers` 和 `body`。

```js
async function postData(newPost) {
  const url = 'https://api.example.com/posts';
  
  const response = await fetch(url, {
    // 1. 设置请求方法为 POST
    method: 'POST', 
    
    // 2. 告诉服务器发送的数据类型是 JSON
    headers: {
      'Content-Type': 'application/json' 
    },
    
    // 3. 将要发送的 JavaScript 对象转换为 JSON 字符串
    body: JSON.stringify(newPost) 
  });
  
  if (!response.ok) {
    throw new Error(`POST 请求失败！状态码: ${response.status}`);
  }

  // 4. 解析服务器返回的响应（通常是创建成功的新资源或状态信息）
  const data = await response.json();
  console.log('新创建的资源:', data);
}

const newPostData = {
  title: '我的第一篇 Fetch 文章',
  body: '这是用 Fetch API 发送的数据内容。',
  userId: 1
};

postData(newPostData).catch(error => console.error(error));
```

## 3. 关键知识点

### A. `Response` 对象的方法

`fetch()` 返回的 `Response` 对象是原始的 HTTP 响应。要获取实际的数据内容（响应体），你需要调用它的一个方法，这些方法都返回一个 **Promise**：

|方法|作用|
|---|---|
|`response.**json()**`|将响应体解析为 **JavaScript 对象**（最常用）。|
|`response.**text()**`|将响应体解析为 **纯文本** 字符串。|
|`response.**blob()**`|将响应体解析为 **二进制数据**（常用于图片/文件）。|
### B. `fetch` 的错误处理陷阱

`fetch()` 的 Promise 只会在**网络故障**（例如断网）或**请求被阻止**时被 **`reject`**（进入 `.catch()` 块）。

**注意：** 如果服务器返回 **`404 Not Found`** 或 **`500 Internal Server Error`**，`fetch()` 的 Promise **仍然会 `resolve`**（进入 `.then()` 块）。你需要手动检查 `Response` 对象的属性来判断请求是否真正成功：

- **`response.ok`**: 一个布尔值，如果状态码在 `200-299` 范围内则为 `true`。
    
- **`response.status`**: 包含 HTTP 状态码（如 200, 404, 500）。
    

因此，在接收到 `response` 后，**务必**加上 `if (!response.ok) { throw ... }` 的检查。