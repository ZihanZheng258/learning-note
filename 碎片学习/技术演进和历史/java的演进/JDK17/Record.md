Java 14 引入了 **`Record`** 类型，这是一种新的类，旨在作为不可变数据载体的简写形式。

- **痛点**：在 Java 中，一个简单的数据类（例如，一个 POJO 或 DTO）通常需要编写大量的样板代码，包括构造函数、==`equals()`、`hashCode()`、`toString()` 以及 getter 方法==。
    
- **`Record` 的改进**：==`Record` 类型可以让你用一行代码来声明一个不可变数据类，编译器会自动为你生成所有这些样板代码。==


```java
public record Point(int x, int y) { }
```

这行代码就相当于自动生成了一个包含 `x` 和 `y` 字段、构造函数、`equals()`、`hashCode()` 和 `toString()` 方法的 `final` 类。

**注意**：`Record` 在 Java 14 和 Java 15 中是预览功能，直到 **Java 16** 才正式成为标准功能。

