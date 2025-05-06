使用 CSS 可以使 HTML 表格更美观。

|Company|Contact|Country|
|---|---|---|
|Alfreds Futterkiste|Maria Anders|Germany|
|Berglunds snabbköp|Christina Berglund|Sweden|
|Centro comercial Moctezuma|Francisco Chang|Mexico|
|Ernst Handel|Roland Mendel|Austria|
|Island Trading|Helen Bennett|UK|
|Königlich Essen|Philip Cramer|Germany|
|Laughing Bacchus Winecellars|Yoshi Tannamuri|Canada|
|Magazzini Alimentari Riuniti|Giovanni Rovelli|Italy|
|North/South|Simon Crowther|UK|
|Paris spécialités|Marie Bertrand|France|
|The Big Cheese|Liz Nixon|USA|
|Vaffeljernet|Palle Ibsen|Denmark|
## 表格边框

指定CSS表格边框，使用border属性。
下面的例子指定了一个表格的Th和TD元素的黑色边框：
```css
table, th, td
{
    border: 1px solid black;
}
```

请注意，在上面的例子中的表格有双边框。这是因为表和th/ td元素有独立的边界。

为了显示一个表的单个边框，使用 border-collapse属性。

## 折叠边框

border-collapse 属性设置表格的边框是否被折叠成一个单一的边框或隔开：

```css
table
{
    border-collapse:collapse;
}
table,th, td
{
    border: 1px solid black;
}
```

## 表格宽度和高度

Width和height属性定义表格的宽度和高度。
下面的例子是设置100％的宽度，50像素的th元素的高度的表格：

```css
table 
{
    width:100%;
}
th
{
    height:50px;
}
```

## 表格文字对齐

表格中的文本对齐和垂直对齐属性。
text-align属性设置水平对齐方式，向左，右，或中心：

```css
td
{
    text-align:right;
}
```

垂直对齐属性设置垂直对齐，比如顶部，底部或中间：

```css
td
{
    height:50px;
    vertical-align:bottom;
}
```

## 表格填充

如需控制边框和表格内容之间的间距，应使用td和th元素的填充属性：

```css
td
{
    padding:15px;
}
```

## 表格颜色

下面的例子指定边框的颜色，和th元素的文本和背景颜色：

```css
table, td, th
{
    border:1px solid green;
}
th
{
    background-color:green;
    color:white;
}
```