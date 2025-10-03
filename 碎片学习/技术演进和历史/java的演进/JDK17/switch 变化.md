这是 Java 12 最值得关注的语言特性之一，它引入了一种更简洁、更安全的 `switch` 语法。

- **旧版 `switch` 的问题**：
    
    - **样板代码多**==：每个 `case` 后面都需要 `break` 语句，忘记写会引发逻辑错误（fall-through）==。
        
    - **返回值复杂**：将 `switch` 作为表达式返回值需要额外的逻辑，不够直观。
        
- **新版 `switch` 的改进**：
    
    - **更简洁的语法**：==使用 `->` 箭头语法来代替传统的 `:` 和 `break`==，这使得代码更精简，并自动避免了 fall-through 的问题。
        
    - ==**可以直接返回值**：`switch` 可以作为表达式直接返回值，==无需在每个 `case` 后面声明变量并赋值。

```java
int day = 3;
String dayType;
switch (day) {
    case 1:
    case 2:
    case 3:
    case 4:
    case 5:
        dayType = "工作日";
        break;
    case 6:
    case 7:
        dayType = "周末";
        break;
    default:
        dayType = "未知";
        break;
}
```


```java
int day = 3;
String dayType = switch (day) {
    case 1, 2, 3, 4, 5 -> "工作日";
    case 6, 7 -> "周末";
    default -> "未知";
};
System.out.println(dayType); // 输出 "工作日"
```


Java 12 引入了新的 `switch` 表达式作为预览功能。在 Java 13 中，它进行了微小的语法调整，并再次作为预览功能发布。

- **主要改动**：
    
    - ==`yield` 关键字的引入：在之前的版本中，当 `case` 分支中有多条语句时，为了返回值，可能需要使用 `break` 语句==，这有点让人困惑。Java 13 引入了 `yield` 关键字来明确地从 `switch` 表达式中返回值，这让代码意图更加清晰


```java
int result = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> 6;
    case TUESDAY                -> 7;
    case THURSDAY, SATURDAY     -> 8;
    case WEDNESDAY -> {
        int temp = day.toString().length();
        yield temp; // 使用 yield 关键字返回值
    }
};
```

尽管 `instanceof` 的模式匹配在 Java 16 中已正式发布，但 Java 17 对其在 `switch` 表达式中的应用进行了**第二次预览**。

- **核心价值**：==这个改进允许你在 `switch` 语句或表达式中直接使用类型模式==，从而消除了繁琐的 `if-else if` 链和类型转换。

```java
Object o = "hello";
String result = switch (o) {
    case Integer i -> "An integer: " + i;
    case String s  -> "A string: " + s;
    default        -> "Something else";
};
System.out.println(result); // 输出 "A string: hello"
```