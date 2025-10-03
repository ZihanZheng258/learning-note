**`__str__(self)` 和 `__repr__(self)`**：当你用 `str()` 或 `print()` 函数将对象转换为字符串时，或者在交互式环境中直接输出对象时，解释器会调用这些方法。

```python
# 解释器看到 print(my_object)
print(my_object) 
# 解释器会调用：my_object.__str__()
```

