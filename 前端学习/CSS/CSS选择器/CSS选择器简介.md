## CSS 选择器

CSS 选择器用于“查找”（或选取）要设置样式的 HTML 元素。
我们可以将 CSS 选择器分为五类

- 简单选择器（根据名称、id、类来选取元素）
- 组合器选择器（根据它们之间的特定关系来选取元素）
- 伪类选择器（根据特定状态选取元素）
- 伪元素选择器（选取元素的一部分并设置其样式）
- 属性选择器（根据属性或属性值来选取元素）

此页会讲解最基本的 CSS 选择器，简单选择器

## CSS 元素选择器

元素选择器根据元素名称来选择 HTML 元素。
### 实例

在这里，页面上的所有 \<p> 元素都将居中对齐，并带有红色文本颜色：
```html
p {
  text-align: center;
  color: red;
}
```

## CSS id 选择器

id 选择器使用 HTML 元素的 id 属性来选择特定元素。

元素的 id 在页面中是唯一的，因此 id 选择器用于选择一个唯一的元素！

要选择具有特定 id 的元素，请写一个井号（＃），后跟该元素的 id。

### 实例

这条 CSS 规则将应用于 id="para1" 的 HTML 元素：
```css
#para1 {
  text-align: center;
  color: red;
}
```

## CSS 类选择器

类选择器选择有特定 class 属性的 HTML 元素。

如需选择拥有特定 class 的元素，请写一个句点（.）字符，后面跟类名。

### 实例

在此例中，所有带有 class="center" 的 HTML 元素将为红色且居中对齐：
```css
.center {
  text-align: center;
  color: red;
}
```
还可以指定只有特定的 HTML 元素会受类的影响。

### 实例

在这个例子中，只有具有 class="center" 的 \<p> 元素会居中对齐：
```css
p.center {
  text-align: center;
  color: red;
}
```
HTML 元素也可以引用多个类。
### 实例

在这个例子中，\<p> 元素将根据 class="center" 和 class="large" 进行样式设置：

```html
<p class="center large">这个段落引用两个类。</p>
```

## CSS 通用选择器

通用选择器（\*）选择页面上的所有的 HTML 元素。

### 实例

下面的 CSS 规则会影响页面上的每个 HTML 元素：

```css
* {
  text-align: center;
  color: blue;
}
```


## CSS 分组选择器

分组选择器选取所有具有相同样式定义的 HTML 元素。

请看下面的 CSS 代码（h1、h2 和 p 元素具有相同的样式定义）：

```css
h1 {
  text-align: center;
  color: red;
}

h2 {
  text-align: center;
  color: red;
}

p {
  text-align: center;
  color: red;
}
```

最好对选择器进行分组，以最大程度地缩减代码,以及减少代码的重复和正交性。

如需对选择器进行分组，请用逗号来分隔每个选择器。

### 实例
在这个例子中，我们对上述代码中的选择器进行分组：
```css
h1, h2, p {
  text-align: center;
  color: red;
}
```

总体结构

| 选择器                   | 例子          | 例子描述                       |
| --------------------- | ----------- | -------------------------- |
| .{class}              | .intro      | 选取所有 class="intro" 的元素。    |
| #{id}                 | \#firstname | 选取 id="firstname" 的那个元素。   |
| {*}                   | *           | 选取所有元素。                    |
| {element}             | p           | 选取所有 \<p> 元素。              |
| {element1},{element2} | div, p      | 选取所有 \<div> 元素和所有 \<p> 元素。 |

