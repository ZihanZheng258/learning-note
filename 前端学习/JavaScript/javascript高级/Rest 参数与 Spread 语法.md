在 JavaScript 中，很多内建函数都支持传入任意数量的参数。

例如：

- `Math.max(arg1, arg2, ..., argN)` —— 返回参数中的最大值。
- `Object.assign(dest, src1, ..., srcN)` —— 依次将属性从 `src1..N` 复制到 `dest`。
- ……等。

在本章中，我们将学习如何编写支持传入任意数量参数的函数，以及如何将数组作为参数传递给这类函数。

## [Rest 参数 `...`](https://zh.javascript.info/rest-parameters-spread#rest-can-shu)

在 JavaScript 中，无论函数是如何定义的，你都可以在调用它时传入任意数量的参数。

例如：
```javascript
function sum(a, b) {
  return a + b;
}

alert( sum(1, 2, 3, 4, 5) );
```

虽然这里这个函数不会因为传入过多的参数而报错。但是，当然，只有前两个参数被求和了。

我们可以在函数定义中声明一个数组来收集参数。语法是这样的：`...变量名`，这将会声明一个数组并指定其名称，其中存有剩余的参数。这三个点的语义就是“收集剩余的参数并存进指定数组中”。

例如，我们需要把所有的参数都放到数组 `args` 中：

```javascript
function sumAll(...args) { // 数组名为 args
  let sum = 0;

  for (let arg of args) sum += arg;

  return sum;
}

alert( sumAll(1) ); // 1
alert( sumAll(1, 2) ); // 3
alert( sumAll(1, 2, 3) ); // 6
```

我们也可以选择将第一个参数获取为变量，并将剩余的参数收集起来。

下面的例子把前两个参数获取为变量，并把剩余的参数收集到 `titles` 数组中

```javascript
function showName(firstName, lastName, ...titles) {
  alert( firstName + ' ' + lastName ); // Julius Caesar

  // 剩余的参数被放入 titles 数组中
  // i.e. titles = ["Consul", "Imperator"]
  alert( titles[0] ); // Consul
  alert( titles[1] ); // Imperator
  alert( titles.length ); // 2
}

showName("Julius", "Caesar", "Consul", "Imperator");
```

## [Spread 语法](https://zh.javascript.info/rest-parameters-spread#spread-syntax)

我们刚刚看到了如何从参数列表中获取数组。
有时候我们也需要做与之相反的事。
例如，内建函数 [Math.max](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Math/max) 会返回参数中最大的值：

```JavaScript
alert( Math.max(3, 5, 1) ); // 5
```

如果我们有一个数组 `[3, 5, 1]`，我们该如何用它调用 `Math.max` 呢？

直接“原样”传入这个数组是不会奏效的，因为 `Math.max` 期望的是列表形式的数值型参数，而不是一个数组：

```JavaScript
let arr = [3, 5, 1];
alert( Math.max(arr) ); // NaN
```

毫无疑问，我们不能手动地去一一设置参数 `Math.max(arg[0], arg[1], arg[2])`，因为我们不确定这儿有多少个。在代码执行时，参数数组中可能有很多个元素，也可能一个都没有。而且，这样的代码也很不优雅。

**Spread 语法** 可以解决这个问题！它看起来和 rest 参数很像，也使用 `...`，但是二者的用途完全相反。

当在函数调用中使用 `...arr` 时，它会把可迭代对象 `arr` “展开”到参数列表中。

以 `Math.max` 为例：

```JavaScript
let arr = [3, 5, 1];

alert( Math.max(...arr) ); // 5（spread 语法把数组转换为参数列表）
```
我们还可以通过这种方式传入多个可迭代对象

```JavaScript
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(...arr1, ...arr2) ); // 8
```
我们甚至还可以将 spread 语法与常规值结合使用：

```JavaScript
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(1, ...arr1, 2, ...arr2, 25) ); // 25
```