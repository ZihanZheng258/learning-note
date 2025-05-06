## 文本颜色

颜色属性被用来设置文字的颜色。

颜色是通过CSS最经常的指定：

- 十六进制值 - 如: **＃FF0000**
- 一个RGB值 - 如: **RGB(255,0,0)**
- 颜色的名称 - 如: **red**

参阅 [CSS 颜色值](https://www.runoob.com/cssref/css-colors-legal.html) 查看完整的颜色值。一个网页的背景颜色是指在主体内的选择：

```css
body {color:red;}
h1 {color:#00ff00;}
h2 {color:rgb(255,0,0);}
```

## 文本的对齐方式

文本排列属性是用来设置文本的水平对齐方式。
文本可居中或对齐到左或右,两端对齐.
当text-align设置为"justify"，每一行被展开为宽度相等，左，右外边距是对齐（如杂志和报纸）。

```css
h1 {text-align:center;}
p.date {text-align:right;}
p.main {text-align:justify;}
```

## 文本修饰

text-decoration 属性用来设置或删除文本的装饰。
从设计的角度看 text-decoration属性主要是用来删除链接的下划线
```css
a {text-decoration:none;}
```
也可以这样装饰文字：
```css
h1 {text-decoration:overline;}
h2 {text-decoration:line-through;}
h3 {text-decoration:underline;}
```
![Remark](https://www.runoob.com/images/lamp.gif) 我们不建议强调指出不是链接的文本，因为这常常混淆用户。

## 文本转换

文本转换属性是用来指定在一个文本中的大写和小写字母。
可用于所有字句变成大写或小写字母，或每个单词的首字母大写。

```css
p.uppercase {text-transform:uppercase;}
p.lowercase {text-transform:lowercase;}
p.capitalize {text-transform:capitalize;}
```

## 文本缩进
文本缩进属性是用来指定文本的第一行的缩进。
```css
p {text-indent:50px;}
```

## 所有CSS文本属性。

|属性|描述|
|:--|:--|
|[color](https://www.runoob.com/cssref/pr-text-color.html)|设置文本颜色|
|[direction](https://www.runoob.com/cssref/pr-text-direction.html)|设置文本方向。|
|[letter-spacing](https://www.runoob.com/cssref/pr-text-letter-spacing.html)|设置字符间距|
|[line-height](https://www.runoob.com/cssref/pr-dim-line-height.html)|设置行高|
|[text-align](https://www.runoob.com/cssref/pr-text-text-align.html)|对齐元素中的文本|
|[text-decoration](https://www.runoob.com/cssref/pr-text-text-decoration.html)|向文本添加修饰|
|[text-indent](https://www.runoob.com/cssref/pr-text-text-indent.html)|缩进元素中文本的首行|
|[text-shadow](https://www.runoob.com/cssref/css3-pr-text-shadow.html)|设置文本阴影|
|[text-transform](https://www.runoob.com/cssref/pr-text-text-transform.html)|控制元素中的字母|
|[unicode-bidi](https://www.runoob.com/cssref/pr-text-unicode-bidi.html)|设置或返回文本是否被重写|
|[vertical-align](https://www.runoob.com/cssref/pr-pos-vertical-align.html)|设置元素的垂直对齐|
|[white-space](https://www.runoob.com/cssref/pr-text-white-space.html)|设置元素中空白的处理方式|
|[word-spacing](https://www.runoob.com/cssref/pr-text-word-spacing.html)|设置字间距|