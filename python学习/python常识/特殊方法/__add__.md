**`__add__(self, other)`**：当你使用 `+` 运算符时，解释器会调用这个方法。

```python
# 解释器看到 a + b
a = MyObject(1)
b = MyObject(2)
c = a + b 
# 解释器会调用：a.__add__(b)
```