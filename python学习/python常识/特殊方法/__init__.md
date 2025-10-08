**`__init__(self, ...)`**：当你创建一个对象实例时，解释器会自动调用这个方法来完成初始化。

```python
# 解释器看到 MyClass() 这个操作
my_object = MyClass() 
# 解释器会调用：MyClass.__init__(my_object)
```