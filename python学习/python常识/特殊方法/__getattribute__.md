### `__getattribute__` 方法：更强的控制

如果你需要对**所有**属性访问进行控制，==无论它们是否存在，你可以使用另一个特殊方法：`__getattribute__(self, name)`。==

这个方法在每次访问属性时都会被调用，包括已存在的属性。因此，它比 `__getattr__` 提供了更强大的功能，但也更危险，因为它很容易导致**无限递归**。

#### 为什么会无限递归？

如果你在 `__getattribute__` 方法内部直接使用 `self.name` 来访问属性，这会再次调用 `__getattribute__`，从而形成无限循环。正确的做法是使用 `super().__getattribute__(name)` 来安全地访问属性。

```python
class SafeDict:
    def __init__(self, data):
        self._data = data

    def __getattribute__(self, name):
        """
        这个方法在每次访问属性时都被调用。
        """
        # 避免无限递归，通过 super() 访问内部数据
        data = super().__getattribute__('_data')
        
        # 如果属性名在我们的数据字典中，返回其值
        if name in data:
            return data[name]
        
        # 否则，调用父类的 __getattribute__ 来处理其他属性（如 __init__等）
        return super().__getattribute__(name)

my_dict = SafeDict({'name': 'Alice', 'age': 30})

# 访问存在的属性
print(f"姓名: {my_dict.name}")  # 输出: 姓名: Alice
print(f"年龄: {my_dict.age}")   # 输出: 年龄: 30
```

在这个例子中，`__getattribute__` 拦截了所有的属性访问，并将它们重定向到内部字典 `_data` 中，==从而实现了类似字典的属性访问。==

### 总结

- **`__getattr__`**：只在**属性不存在时**才被调用。这是实现你原始需求（为缺失属性提供默认值）的最佳选择，因为它更安全、更高效。
    
- **`__getattribute__`**：**每次访问属性时**都调用。它提供了对属性访问的完全控制，但使用时必须非常小心，以防止无限递归。
    

所以，对于你的问题，你应该使用 `__getattr__` 来优雅地实现 `dog.name` 在找不到 `name` 属性时返回一个默认值 `0` 的行为。