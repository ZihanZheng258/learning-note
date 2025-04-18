
## 标签定义及使用说明

<base> 标签为页面上的所有的相对链接规定默认 URL 或默认目标。

在一个文档中，最多能使用一个 \<base> 元素。\<base>标签必须位于 \<head> 元素内部。

## 提示和注释

提示：请把 \<base> 标签排在 \<head> 元素中_第一个_元素的位置，这样 head 区域中其他元素就可以使用 \<base> 元素中的信息了。

**注释：**如果使用了\<base>标签，则必须具备 href 属性或者 target 属性或者两个属性都具备。

<head> <base href="http://www.runoob.com/images/" target="_blank"> </head> <body> <img src="logo.png" width="24" height="39" alt="Stickman"> <a href="http://www.runoob.com">runoob.com</a> </body>
