在 Python 中，`get` 和 `set` 并不是像 Java 或 C++ 那样内置的关键字。它们通常指的是**获取（get）**和**设置（set）**对象属性值的操作，是面向对象编程中**封装（Encapsulation）**概念的实现。

Python 有两种主要的方法来实现这种功能：

1. **传统的 Getter 和 Setter 方法**（不推荐）
    
2. **使用 `@property` 装饰器**（推荐的 Pythonic 方法）


### 1. 传统的 Getter 和 Setter 方法

这种方法是许多其他语言（如 Java）的程序员所熟悉的。它通过定义显式的 `get_...` 和 `set_...` 方法来访问或修改私有属性。

**优点:** 逻辑清晰，对初学者友好。

**缺点:** 这种方法在 Python 中被认为是**不优雅且多余的**。它增加了大量样板代码，违背了 Python 的简洁哲学。

```python
class Circle:
    def __init__(self, radius):
        # 约定俗成的私有属性，不应该直接访问
        self._radius = radius

    def get_radius(self):
        return self._radius

    def set_radius(self, radius):
        if radius < 0:
            raise ValueError("半径不能为负数！")
        self._radius = radius

# 使用传统的 getter/setter
my_circle = Circle(10)
print(my_circle.get_radius())  # 获取值

my_circle.set_radius(20)      # 设置值
print(my_circle.get_radius())

# 试图设置非法值
try:
    my_circle.set_radius(-5)
except ValueError as e:
    print(e)
```

### 2. 使用 `@property` 装饰器（推荐）

这是 Pythonic（Python 风格）的实现方式。==`@property` 装饰器允许你在类的内部使用方法，但从外部看，它们就像是普通的属性一样。==

这种方式的**核心思想**是：先创建一个简单的公共属性，如果之后需要添加验证、计算或其他逻辑，==你可以将它转换为一个**属性（property）**，而**无需改变任何调用它的外部代码**。==

`@property` 主要由三部分组成：

- **`@property`**: 定义一个 getter 方法。
    
- **`@<方法名>.setter`**: 定义一个 setter 方法。
    
- **`@<方法名>.deleter`**: 定义一个 deleter 方法

#### 示例代码

让我们用 `@property` 改造上面的 `Circle` 类。

``` python
class Circle:
    def __init__(self, radius):
        # 初始化时调用 setter 方法进行验证
        self.radius = radius

    # Getter: 获取 radius 的值
    @property
    def radius(self):
        print("正在获取半径...")
        return self._radius

    # Setter: 设置 radius 的值
    @radius.setter
    def radius(self, value):
        print("正在设置半径...")
        if value < 0:
            raise ValueError("半径不能为负数！")
        self._radius = value
        
    # Deleter: 删除 radius 属性
    @radius.deleter
    def radius(self):
        print("正在删除半径...")
        del self._radius

# 使用 @property
my_circle = Circle(10)

# 从外部看，它就是普通属性的访问和修改
print(my_circle.radius)      # 自动调用 @property getter
my_circle.radius = 20        # 自动调用 @radius.setter
print(my_circle.radius)

# 试图设置非法值
try:
    my_circle.radius = -5
except ValueError as e:
    print(e)

# 删除属性
del my_circle.radius
# print(my_circle.radius)    # 再次访问会引发 AttributeError
```

### 总结

- **`@property` 的好处**：==它将方法的逻辑隐藏在简单的属性访问语法背后，让你的代码更简洁、更具可读性==。最重要的是，它允许你在不改变外部代码的情况下，从一个简单的公共属性平滑地过渡到拥有复杂逻辑的 `getter`/`setter`。
    
- **何时使用 `get`/`set`**：在 Python 中，我们通常只在需要对属性进行**验证、计算或触发副作用**时才使用 `setter` 和 `getter`（通过 `@property` 实现）。==如果一个属性不需要任何特殊逻辑，就直接将它声明为公共属性即可。==
    

总而言之，**Python 的哲学是“先让它工作，必要时再添加限制”**。我们不写多余的代码，==而是使用 `@property` 这种强大的工具，在需要时为属性赋予 `get`/`set` 的能力。==