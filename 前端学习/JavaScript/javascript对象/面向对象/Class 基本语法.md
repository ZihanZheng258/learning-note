在日常开发中，我们经常需要创建许多相同类型的对象，例如用户（users）、商品（goods）或者任何其他东西。

正如我们在 [构造器和操作符 "new"](https://zh.javascript.info/constructor-new) 一章中已经学到的，`new function` 可以帮助我们实现这种需求。

但在现代 JavaScript 中，还有一个更高级的“类（class）”构造方式，它引入许多非常棒的新功能，这些功能对于面向对象编程很有用。

## [“class” 语法](https://zh.javascript.info/class#class-yu-fa)

基本语法是：
```JavaScript
class MyClass {
  // class 方法
  constructor() { ... }
  method1() { ... }
  method2() { ... }
  method3() { ... }
  ...
}
```

然后使用 `new MyClass()` 来创建具有上述列出的所有方法的新对象。

`new` 会自动调用 `constructor()` 方法，因此我们可以在 `constructor()` 中初始化对象。

例如：