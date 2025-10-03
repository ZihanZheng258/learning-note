好的，网页端的 **DOM** 和 **BOM** 是前端开发中最基础也是最重要的两个概念。==它们都是浏览器提供给 JavaScript 语言的接口（API），让 JavaScript 能够与网页内容和浏览器窗口本身进行交互。==

## 1. DOM (Document Object Model)

**DOM** 的中文名称是 **文档对象模型**。

### 核心作用：操作网页内容

DOM 允许 JavaScript 访问和修改网页的**内容**、**结构**和**样式**。

### 原理和结构

你可以把 DOM 理解为浏览器将一个 HTML 文档解析后，在内存中创建的一个**树状结构**。

- **节点 (Node)**：==树中的每一个部分都称为一个节点。==
    
- **元素节点 (Element Node)**：HTML 标签，如 `<body>`、`<div>`、`<p>`。
    
- **文本节点 (Text Node)**：==标签中的文字内容==。
    
- **属性节点 (Attribute Node)**：标签的属性，如 `id="main"`、`class="btn"`。

**简而言之，DOM 是关于网页“内容”的一切。** 你用 JavaScript 来添加、删除、移动 HTML 元素，或者改变它们的 CSS 样式，都是通过 DOM 来实现的。

## 2. BOM (Browser Object Model)

**BOM** 的中文名称是 **浏览器对象模型**。

### 核心作用：操作浏览器窗口和环境

BOM ==允许 JavaScript 访问和操作**浏览器窗口**本身以及浏览器提供的**环境功能**，而不是网页的具体内容==。

### 原理和结构

BOM 的核心是 **`window` 对象**。==`window` 对象是所有 JavaScript 全局对象的**顶层对象**。==

**一个重要的知识点：** DOM 的入口对象 `document` 实际上是 `window` 对象的属性之一（即 `window.document`）。所以，**BOM 包含了 DOM**。