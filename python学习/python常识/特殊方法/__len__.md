**`__len__(self)`**：当你对一个对象使用 `len()` 函数时，解释器会调用这个方法来获取对象的长度

```python
# 解释器看到 len(my_list)
my_list = [1, 2, 3]
print(len(my_list))
# 解释器会调用：my_list.__len__()
```
