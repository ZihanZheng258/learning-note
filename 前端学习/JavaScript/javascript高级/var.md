在本教程最开始那部分的 [变量](https://zh.javascript.info/variables) 这章中，我们提到了变量声明的三种方式：

1. `let`
2. `const`
3. `var`

`var` 声明与 `let` 相似。大部分情况下，我们可以用 `let` 代替 `var` 或者 `var` 代替 `let`，都能达到预期的效果

但实际上 `var` 却是一头非常不同的，源自远古时代的怪兽。在现代脚本中一般不再使用它，但它仍然潜伏在旧脚本中。

如果你不打算接触这样的脚本，你甚至可以跳过本章或推迟阅读本章。

另一方面，了解将旧脚本从 `var` 迁移到 `let` 时的区别，以避免奇怪的错误，是很重要的。

## [“var” 没有块级作用域](https://zh.javascript.info/var#var-mei-you-kuai-ji-zuo-yong-yu)

用 `var` 声明的变量，不是函数作用域就是全局作用域。它们在代码块外也是可见的（译注：也就是说，`var` 声明的变量只有函数作用域和全局作用域，没有块级作用域）。

```javascript
if (true) {
  var test = true; // 使用 "var" 而不是 "let"
}

alert(test); // true，变量在 if 结束后仍存在
```

## [“var” 允许重新声明](https://zh.javascript.info/var#var-yun-xu-chong-xin-sheng-ming)

如果我们用 `let` 在同一作用域下将同一个变量声明两次，则会出现错误：

使用 `var`，我们可以重复声明一个变量，不管多少次都行。如果我们对一个已经声明的变量使用 `var`，这条新的声明语句会被忽略：

## [“var” 声明的变量，可以在其声明语句前被使用](https://zh.javascript.info/var#var-sheng-ming-de-bian-liang-ke-yi-zai-qi-sheng-ming-yu-ju-qian-bei-shi-yong)

当函数开始的时候，就会处理 `var` 声明（脚本启动对应全局变量）。

换言之，`var` 声明的变量会在函数开头被定义，与它在代码中定义的位置无关（这里不考虑定义在嵌套函数中的情况）。

那么看一下这段代码：

```javascript
function sayHi() {
  phrase = "Hello";

  alert(phrase);

  var phrase;
}
sayHi();
```

……从技术上讲，它与下面这种情况是一样的（`var phrase` 被上移至函数开头）：

```javascript
function sayHi() {
  var phrase;

  phrase = "Hello";

  alert(phrase);
}
sayHi();
```

……甚至与这种情况也一样（记住，代码块是会被忽略的）：

```javascript
function sayHi() {
  phrase = "Hello"; // (*)

  if (false) {
    var phrase;
  }

  alert(phrase);
}
sayHi();
```

人们将这种行为称为“提升”（英文为 “hoisting” 或 “raising”），因为所有的 `var` 都被“提升”到了函数的顶部。

所以，在上面的例子中，`if (false)` 分支永远都不会执行，但没关系，它里面的 `var` 在函数刚开始时就被处理了，所以在执行 `(*)` 那行代码时，变量是存在的。

**声明会被提升，但是赋值不会。**