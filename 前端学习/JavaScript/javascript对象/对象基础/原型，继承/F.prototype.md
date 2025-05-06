我们还记得，可以使用诸如 `new F()` 这样的构造函数来创建一个新对象。

如果 `F.prototype` 是一个对象，那么 `new` 操作符会使用它为新对象设置 `[[Prototype]]`

请注意：

JavaScript 从一开始就有了原型继承。这是 JavaScript 编程语言的核心特性之一。

但是在过去，没有直接对其进行访问的方式。唯一可靠的方法是本章中会介绍的构造函数的 `"prototype"` 属性。目前仍有许多脚本仍在使用它。

请注意，这里的 `F.prototype` 指的是 `F` 的一个名为 `"prototype"` 的常规属性。这听起来与“原型”这个术语很类似，但这里我们实际上指的是具有该名字的常规属性。

下面是一个例子：
```JavaScript
let animal = {
  eats: true
};

function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype = animal;

let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal

alert( rabbit.eats ); // true
```

设置 `Rabbit.prototype = animal` 的字面意思是：“当创建了一个 `new Rabbit` 时，把它的 `[[Prototype]]` 赋值为 `animal`”。

## [默认的 F.prototype，构造器属性](https://zh.javascript.info/function-prototype#mo-ren-de-fprototype-gou-zao-qi-shu-xing)

每个函数都有 `"prototype"` 属性，即使我们没有提供它。

默认的 `"prototype"` 是一个只有属性 `constructor` 的对象，属性 `constructor` 指向函数自身。

像这样：

```JavaScript
function Rabbit() {}

/* 默认的 prototype
Rabbit.prototype = { constructor: Rabbit };
*/
```
```JavaScript
function Rabbit() {}
// 默认：
// Rabbit.prototype = { constructor: Rabbit }

alert( Rabbit.prototype.constructor == Rabbit ); // true
```
通常，如果我们什么都不做，`constructor` 属性可以通过 `[[Prototype]]` 给所有 rabbits 使用：

```JavaScript
function Rabbit() {}
// 默认：
// Rabbit.prototype = { constructor: Rabbit }

let rabbit = new Rabbit(); // 继承自 {constructor: Rabbit}

alert(rabbit.constructor == Rabbit); // true (from prototype)
```
我们可以使用 `constructor` 属性来创建一个新对象，该对象使用与现有对象相同的构造器。
