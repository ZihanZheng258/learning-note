```
    1）声明在函数内部，是函数的本地值，类似private
    2）声明在函数外部，是对当前包可见(包内所有.go文件都可见)的全局值，类似protect
    3）声明在函数外部且首字母大写是所有包可见的全局值,类似public
```

## Go语言声明
```
  var（声明变量）, const（声明常量）, type（声明类型） ,func（声明函数）。
```

Go的程序是保存在多个.go文件中，文件的第一行就是package XXX声明，用来说明该文件属于哪个包(package)，package声明下来就是import声明，再下来是类型，变量，常量，函数的声明。

## Go项目构建及编译
一个Go工程中主要包含以下三个目录：
```
    src：源代码文件
    pkg：包文件
    bin：相关bin文件
```

## 值类型：
```
    bool
    int(32 or 64), int8, int16, int32, int64
    uint(32 or 64), uint8(byte), uint16, uint32, uint64
    float32, float64
    string
    complex64, complex128
    array    -- 固定长度的数组
```