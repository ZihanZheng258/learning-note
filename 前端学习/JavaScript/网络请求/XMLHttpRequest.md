
**`XMLHttpRequest` (XHR)** 是一个内建在浏览器中的 **JavaScript 对象**，它允许客户端（网页）在不刷新整个页面的情况下，向服务器发送 HTTP 请求并接收响应。

尽管它的名字中带有“XML”，但它实际上能够处理**任何类型的数据**（包括 JSON、纯文本、HTML 等）。

## XHR 的历史地位和作用

`XMLHttpRequest` 是 **AJAX（Asynchronous JavaScript and XML）** 技术的核心组成部分。

在 AJAX 出现之前，网页如果要更新数据，唯一的办法就是提交表单并刷新整个页面。==XHR 的出现彻底改变了 Web 交互模式，使得网页可以进行**异步数据交换**，只更新页面的局部内容，极大地提升了用户体验，催生了现代的“单页应用 (SPA)”概念。==

### XHR 的主要特点

|特点|描述|
|---|---|
|**异步通信**|XHR 默认以异步方式工作，这意味着请求发送后，JavaScript 线程不会阻塞，用户可以继续与页面交互。|
|**细粒度控制**|它提供了对请求头部、响应状态、进度追踪（上传和下载）等的完整控制。|
|**事件驱动**|XHR 通过监听各种事件（如 `onload`、`onprogress`、`onerror`）来处理请求生命周期中的不同状态。|
|**同源策略**|XHR 遵守浏览器的同源策略，跨源请求需要服务器配置 CORS 才能成功。|

---

## XHR 的生命周期（流程）

使用 XHR 发送请求通常涉及以下几个步骤和方法：

1. **创建对象：**
    
    ```js
    const xhr = new XMLHttpRequest();
    ```
    
2. **配置请求：** 使用 `open()` 方法指定请求的类型、URL 和是否异步。
    
    ```js
    xhr.open('GET', 'https://api.example.com/data', true); // 异步
    ```
    
3. **设置头部和凭证：** 可选步骤，用于设置自定义的请求头部或开启 `cookie` 凭证。
    
    ```js
    xhr.setRequestHeader('Content-Type', 'application/json');
    xhr.withCredentials = true;
    ```
    
4. **设置事件监听：**
    
    - `xhr.onload`：请求成功完成且响应已下载。
        
    - `xhr.onprogress`：请求过程中触发，用于追踪上传或下载进度。
        
    - `xhr.onerror`：请求发生错误（如网络问题）。
        
    - `xhr.onreadystatechange`：请求状态变化时触发（较老的方法）。
        
5. **发送请求：**
    
    ```js
    xhr.send(data); // GET 请求通常传入 null，POST 请求传入请求体数据
    ```
    

---

## XHR 与 `fetch` 的比较

虽然 `XMLHttpRequest` 是 Web 异步通信的基石，但在现代 JavaScript 开发中，它在很大程度上已经被 **`fetch` API** 取代。

|特性|`XMLHttpRequest` (XHR)|`fetch` API|
|---|---|---|
|**语法**|基于事件和回调函数，写法复杂。|基于 **Promise**，使用 `async/await`，语法更简洁清晰。|
|**处理数据**|必须手动解析 JSON (`JSON.parse(xhr.responseText)`)。|内置各种方法 (`response.json()`, `response.text()`)，更易用。|
|**上传/下载进度**|内置 `xhr.upload.onprogress`，**易于追踪上传进度。**|只能通过 **`ReadableStream`** 追踪下载进度，**追踪上传进度非常困难**。|
|**中断请求**|使用 `xhr.abort()`，非常直接。|使用 `AbortController` 和 `signal`，稍微复杂但更现代。|


### 结论

在大多数数据请求场景中，**`fetch` API 是首选**。但是，==如果你需要**可靠且简单的上传进度追踪功能**（例如显示文件上传的百分比），`XMLHttpRequest` 仍然是目前最直接和最方便的选择。==