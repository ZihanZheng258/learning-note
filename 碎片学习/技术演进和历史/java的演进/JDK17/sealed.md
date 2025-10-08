在 Java 15 和 16 中作为预览功能发布的 `sealed` 类和接口，在 Java 17 中正式成为标准。

- **核心价值**：==`sealed` 允许你显式地声明一个类或接口可以被哪些子类或实现类继承==。这解决了在设计类结构时，既想要利用继承，又想对其进行严格控制的痛点。

**工作方式**：你使用 `sealed` 关键字来声明一个类，并用 `permits` 关键字指定可以继承它的类。

```java
public sealed class Shape permits Circle, Square, Triangle {
    // ...
}
final class Circle extends Shape {}
final class Square extends Shape {}
class Triangle extends Shape {} // 这里的子类必须是 final, sealed, 或 non-sealed
```

