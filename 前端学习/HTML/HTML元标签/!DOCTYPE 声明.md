\<!DOCTYPE>声明有助于浏览器中正确显示网页。
网络上有很多不同的文件，如果能够正确声明HTML的版本，浏览器就能正确显示网页内容。
doctype 声明是不区分大小写的

==**不同HTML可以以这些方式声明**==
### HTML5

`<!DOCTYPE html>`

### HTML 4.01

`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"`  
`"http://www.w3.org/TR/html4/loose.dtd">`

### XHTML 1.0

`<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"`  
`"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">`



以上代码是 HTML5 的文档类型声明，它告诉浏览器当前页面是使用 HTML5 规范编写的，HTML5 是最新的 HTML 版本，拥有更多的功能和优化，因此推荐在新的 Web 页面中使用它。

在 HTML 文档中，\<!DOCTYPE> 声明通常是文档的第一行，位于 \<html>标签之前。

#### 详细介绍
在 HTML 中，\<!DOCTYPE> 声明是文档类型声明（Document Type Declaration）的缩写，用于指示浏览器当前页面使用的HTML版本。

\<!DOCTYPE> 声明位于文档中的最前面的位置，处于 \<html> 标签之前。

\<!DOCTYPE> 声明不是一个 HTML 标签，因此不需要关闭标签。。

在 HTML 4.01 及之前版本中，\<!DOCTYPE> 声明可能会指向 DTD（文档类型定义）文件，它定义了文档的结构和元素规则。因为 HTML 4.01 是基于 SGML （Standard Generalized Markup Language 标准通用标记语言）。DTD 指定了标记语言的规则，确保了浏览器能够正确的渲染内容。

HTML5 不是基于 SGML，因此不再依赖 DTD 文件，而是使用更简单的声明。

**提示：**总是给您的 HTML 文档添加 <!DOCTYPE> 声明，确保浏览器能够预先知道文档类型。