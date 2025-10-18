
`fetch()` 函数接受**两个参数**：一个是**必选**的资源路径，另一个是**可选**的配置对象。

---

## 1. 第一个参数：`resource` (必选)

这个参数指定了你要获取的资源。它可以是以下两者之一：

1. **URL 字符串：** 资源的地址，例如 `'https://api.example.com/data'`。
    
2. **`Request` 对象：** 一个完整的请求对象，它已经包含了 URL 和所有配置==（在这种情况下，第二个参数 `init` 将被忽略）。==

## 2. 第二个参数：`init` (可选的配置对象)

这个参数是一个配置对象，用于定义请求的所有自定义设置。它是控制请求行为的关键。

### A. 常用配置项（HTTP 请求核心）

|参数名|类型|默认值|作用|
|---|---|---|---|
|**`method`**|字符串|`'GET'`|要使用的 HTTP 方法，如 `'POST'`、`'PUT'`、`'DELETE'` 等。|
|**`headers`**|对象/`Headers`|`{}`|包含自定义 HTTP 请求头，如 `{'Content-Type': 'application/json'}`。|
|**`body`**|任意类型|`null`|请求主体，用于 **`POST`、`PUT`** 等发送数据的请求。通常是经过 `JSON.stringify()` 处理的 JSON 字符串或 `FormData` 对象。|
|**`signal`**|`AbortSignal`|`null`|用于 **取消** 正在进行的请求。它与 `AbortController` 搭配使用。|

导出到 Google 表格

### B. 跨域与凭证控制

|参数名|类型|默认值|作用|
|---|---|---|---|
|**`mode`**|字符串|`'cors'`|请求模式： - `'cors'` (默认，允许跨域) - `'no-cors'` (用于访问同源资源的子集) - `'same-origin'` (只允许同源请求)|
|**`credentials`**|字符串|`'same-origin'`|浏览器是否发送 cookies、HTTP 认证信息等凭证： - `'omit'` (不发送凭证) - `'same-origin'` (仅对同源请求发送) - `'include'` (对跨域和同源请求都发送)|
|**`cache`**|字符串|`'default'`|如何处理请求的缓存： - `'default'`、`'no-store'`、`'reload'` 等。|

导出到 Google 表格

### C. 其他控制项

|参数名|类型|默认值|作用|
|---|---|---|---|
|**`redirect`**|字符串|`'follow'`|如何处理 HTTP 重定向响应： - `'follow'` (自动跟随重定向，默认) - `'error'` (遇到重定向就抛出错误) - `'manual'` (手动处理重定向)|
|**`referrer`**|字符串|`'no-referrer-when-downgrade'`|指定 HTTP Referer 标头的值。|
|**`integrity`**|字符串|`''`|用于子资源完整性（SRI）检查。|
|**`keepalive`**|布尔值|`false`|允许请求在页面关闭或跳转后仍保持活动状态。|
### 1. 简单的 GET 请求

默认情况下，只需第一个参数。

```js
fetch('https://api.example.com/users'); // method: 'GET', mode: 'cors'
```

### 2. 复杂的 POST 请求（提交 JSON 数据）

需要利用第二个参数配置 `method`、`headers` 和 `body`。

```js
const userData = { name: 'Alice', age: 30 };

fetch('https://api.example.com/users', {
  method: 'POST', // 设置请求方法
  headers: {
    'Content-Type': 'application/json' // 告诉服务器请求体是 JSON
  },
  body: JSON.stringify(userData) // 将 JS 对象转换为 JSON 字符串
})
.then(response => response.json())
.then(data => console.log('Success:', data));
```