当浏览器读到样式表时，它将根据样式表中的信息来格式化 HTML 文档。

## 三种使用 CSS 的方法

有三种插入样式表的方法：

- 外部 CSS
- 内部 CSS
- 行内 CSS

## 外部 CSS

通过使用外部样式表，您只需修改一个文件即可改变整个网站的外观！

每张 HTML 页面必须在 head 部分的 \<link> 元素内包含对外部样式表文件的引用。

### 实例

外部样式在 HTML 页面 \<head> 部分内的 \<link> 元素中进行定义：
```html
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

外部样式表可以在任何文本编辑器中编写，并且必须以 .css 扩展名保存。

外部 .css 文件不应包含任何 HTML 标签。


## 内部 CSS

如果一张 HTML 页面拥有唯一的样式，那么可以使用内部样式表。
内部样式是在 head 部分的 \<style> 元素中进行定义。

### 实例
内部样式在 HTML 页面的 \<head> 部分内的 \<style> 元素中进行定义：

```html
<!DOCTYPE html>
<html>
<head>
<style>
body {
  background-color: linen;
}

h1 {
  color: maroon;
  margin-left: 40px;
} 
</style>
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

## 行内 CSS

行内样式（也称内联样式）可用于为单个元素应用唯一的样式。

如需使用行内样式，请将 style 属性添加到相关元素。style 属性可包含任何 CSS 属性。

### 实例
行内样式在相关元素的 "style" 属性中定义：
```html
<!DOCTYPE html>
<html>
<body>

<h1 style="color:blue;text-align:center;">This is a heading</h1>
<p style="color:red;">This is a paragraph.</p>

</body>
</html>
```

行内样式失去了样式表的许多优点（通过将内容与呈现混合在一起）。请谨慎使用此方法。

## 多个样式表

如果在不同样式表中为同一选择器（元素）定义了一些属性，则将使用最后读取的样式表中的值。 

例子：
如果内部样式是在链接到外部样式表_之后_定义的，则 \<h1> 元素将是橙色：
```html
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
<style>
h1 {
  color: orange;
}
</style>
</head>
```

不过，如果在链接到外部样式表_之前_定义了内部样式，则 \<h1> 元素将是深蓝色：

```html
<head>
<style>
h1 {
  color: orange;
}
</style>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

