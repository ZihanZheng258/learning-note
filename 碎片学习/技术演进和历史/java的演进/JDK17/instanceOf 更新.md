**旧版 `instanceof` 的痛点**：在 Java 14 之前，当你使用 `instanceof` 检查一个对象的类型后，通常需要手动将它向下转型为一个新的变量，这导致了重复的代码和不必要的变量声明。

```java
if (obj instanceof String) {
    String s = (String) obj; // 手动向下转型
    System.out.println(s.length());
}
```

**模式匹配的改进**：`instanceof` 的模式匹配允许你在一个表达式中同时进行类型检查和变量绑定。

```java
if (obj instanceof String s) { // 类型检查并绑定到变量 s
    System.out.println(s.length());
}
```