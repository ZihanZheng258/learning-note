`explicit` 是 C++ 中的一个关键字，用于**防止隐式类型转换 (implicit type conversion)**。它主要应用于以下两个地方：

1. **构造函数 (Constructors)**
2. **转换函数 (Conversion Functions) (C++11 引入)**


### 1. `explicit` 用于构造函数

当一个类有一个**只接受一个参数的构造函数**时，==这个构造函数可能会被编译器用于隐式地将该参数类型转换为类类型。这通常被称为“**转换构造函数 (converting constructor)**”。==


**问题所在：** 这种隐式转换有时会导致意想不到的行为或逻辑错误，使代码难以理解和调试。

==**`explicit` 的作用：** 将构造函数声明为 `explicit` 可以阻止这种隐式转换。==这意味着，你只能通过**直接初始化 (direct initialization)** 或**显式类型转换 (explicit cast)** 来调用这个构造函数。

``` C++
#include <iostream>
#include <string>

class MyClass {
public:
    // 非 explicit 构造函数
    MyClass(int i) : value(i) {
        std::cout << "MyClass(int) called with value: " << value << std::endl;
    }

    // explicit 构造函数
    explicit MyClass(double d) : value(static_cast<int>(d)) {
        std::cout << "explicit MyClass(double) called with value: " << value << std::endl;
    }

    // explicit 构造函数，使用 initializer_list (C++11+)
    explicit MyClass(std::initializer_list<int> list) {
        if (!list.empty()) {
            value = *list.begin();
        } else {
            value = 0;
        }
        std::cout << "explicit MyClass(initializer_list) called with value: " << value << std::endl;
    }

    void print() const {
        std::cout << "Current value: " << value << std::endl;
    }

private:
    int value;
};

void processMyClass(MyClass obj) {
    std::cout << "Inside processMyClass." << std::endl;
    obj.print();
}

int main() {
    // 1. 对于非 explicit 构造函数 MyClass(int)

    // 允许隐式转换 (复制初始化)
    MyClass obj1 = 10; // 编译器将 10 隐式转换为 MyClass 类型
    obj1.print();
    // 输出: MyClass(int) called with value: 10
    // 输出: Current value: 10

    // 允许隐式转换 (函数参数)
    processMyClass(20); // 编译器将 20 隐式转换为 MyClass 类型
    // 输出: MyClass(int) called with value: 20
    // 输出: Inside processMyClass.
    // 输出: Current value: 20

    // 允许直接初始化
    MyClass obj3(30);
    obj3.print();
    // 输出: MyClass(int) called with value: 30
    // 输出: Current value: 30

    std::cout << "--------------------" << std::endl;

    // 2. 对于 explicit 构造函数 MyClass(double)

    // 不允许隐式转换 (编译错误！)
    // MyClass obj4 = 3.14; // Error: no viable conversion from 'double' to 'MyClass'
    // processMyClass(4.56); // Error: no viable conversion from 'double' to 'MyClass'

    // 必须使用直接初始化
    MyClass obj5(3.14);
    obj5.print();
    // 输出: explicit MyClass(double) called with value: 3
    // 输出: Current value: 3

    // 必须使用显式类型转换
    MyClass obj6 = static_cast<MyClass>(4.56);
    obj6.print();
    // 输出: explicit MyClass(double) called with value: 4
    // 输出: Current value: 4

    std::cout << "--------------------" << std::endl;

    // 3. 对于 explicit 构造函数 MyClass(std::initializer_list<int>)

    // 不允许隐式转换 (编译错误！)
    // MyClass obj7 = {7, 8}; // Error: no viable conversion from 'initializer_list<int>' to 'MyClass'

    // 必须使用列表初始化语法
    MyClass obj8{7, 8}; // OK
    obj8.print();
    // 输出: explicit MyClass(initializer_list) called with value: 7
    // 输出: Current value: 7

    return 0;
}
```

**何时使用 `explicit`：**

- **几乎所有单参数构造函数都应该声明为 `explicit`。** 这是一种好的实践，因为它强制程序员明确地进行类型转换，从而减少潜在的错误和不确定性。

- **当构造函数的功能是“转换”而不是“初始化”时。** 例如，如果你有一个 `String` 类，并且它有一个接受 `const char*` 的构造函数，那么这个构造函数很可能会被用于将 C 风格字符串隐式转换为 `String` 对象，这通常是期望的行为，此时可能不需要 `explicit`。

- **当转换是“有损的”或“不直观的”时。** 比如上面的 `MyClass(double)`，将 `double` 转换为 `int` 可能会丢失小数部分，这种转换最好是显式的。