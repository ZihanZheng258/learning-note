这是一个非常关键的技术点，因为你提到了两种不同的 HTTP 请求体格式：

1. 用于文件上传的 **`multipart/form-data`** 格式。
    
2. 用于结构化数据交换的 **`application/json`** 格式。
    

**简短的回答是：HTTP 请求体是**排他性**的，你**不能**同时使用 `multipart/form-data` 和一个独立的 JSON 请求体。你需要将所有数据（包括 JSON 数据）合并到 `multipart/form-data` 请求中。**

## 为什么不能同时使用？

HTTP 请求只能有一个 **`Content-Type`** 头部，而这个头部决定了请求体 (Request Body) 的**整体格式**。

|`Content-Type`|请求体格式|主要用途|
|---|---|---|
|**`application/json`**|整个请求体必须是一个有效的 JSON 字符串。|纯数据交换，不能上传文件。|
|**`multipart/form-data`**|整个请求体是一个由边界分隔的多个数据段（Part）的集合。|必须用于文件上传，可以包含其他文本字段。|
如果你正在上传文件，你的 `Content-Type` **必须** 是 `multipart/form-data`。因此，你不能再发送一个单独的==、纯粹的 `application/json` 格式的请求体。==

## 如何在 `multipart/form-data` 中发送 JSON 数据？

虽然你不能发送一个独立的 JSON 请求体，但你可以将 JSON 数据作为 `multipart/form-data` 的**一个普通字段**发送。

### 步骤 1: 将 JSON 对象转换为字符串

首先，你需要像对待任何其他表单字段一样，将你的 JSON 数据对象转换为一个字符串。

```js
const fileInput = document.getElementById('myFileInput'); 
const myFile = fileInput.files[0]; 

// 你想发送的 JSON 数据
const metadata = {
    title: "我的上传文件",
    description: "这是文件的附加信息",
    category: "docs"
};

// 转换为 JSON 字符串
const metadataJsonString = JSON.stringify(metadata);
```

### 步骤 2: 将 JSON 字符串添加到 `FormData`

使用 `formData.append()` 将 JSON 字符串作为一个普通字段添加到 `FormData` 中。

```js
const formData = new FormData();

// 1. 将文件添加到 formData
formData.append('file', myFile);

// 2. 将 JSON 字符串作为一个普通字段添加
// 约定字段名为 'metadata' 或 'json_data'
formData.append('metadata', metadataJsonString);
```

步骤 3: 发送请求
```js
fetch('https://api.example.com/upload-with-json', {
    method: 'POST',
    // 再次提醒：不要手动设置 Content-Type！
    body: formData 
})
.then(response => response.json())
.then(result => console.log('上传成功:', result))
.catch(error => console.error('错误:', error));
```

### 服务器端的处理

服务器在接收到这个 `multipart/form-data` 请求后，需要做两件事：

1. 解析名为 `'file'` 的数据段，将文件保存到存储空间。
    
2. 解析名为 `'metadata'` 的数据段，得到一个 JSON **字符串**。服务器代码必须再次调用一个 JSON 解析函数（例如 Node.js 中的 `JSON.parse()`）才能将其转换回可用的对象。
    

通过这种方法，你就可以在一个 HTTP 请求中同时传输文件和复杂的 JSON 结构化数据了。