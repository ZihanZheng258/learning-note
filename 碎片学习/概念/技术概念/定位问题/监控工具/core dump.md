分析core dump是Linux应用程序调试的一种有效方式，core dump又称为“核心转储”，==是该进程实际使用的物理内存的“快照”。==分析core dump文件可以获取应用程序崩溃时的现场信息，如程序运行时的CPU寄存器值、堆栈指针、栈数据、函数调用栈等信息。

Core dump是Linux基于[信号](https://zhida.zhihu.com/search?content_id=202468959&content_type=Article&match_order=1&q=%E4%BF%A1%E5%8F%B7&zhida_source=entity)实现的。==Linux中信号是一种异步事件处理机制==，每种信号都对应有默认的异常处理操作，默认操作包括忽略该信号（Ignore）、暂停进程（Stop）、终止进程（Terminate）、终止并产生core dump（Core）等。

## **什么情况下会产生core dump呢？**

以下情况会出现应用程序崩溃导致产生core dump：

1. 内存访问越界 ==（数组越界、字符串无\n结束符、字符串读写越界）==
2. 多线程程序中使用了==线程不安全的函数，如[[不可重入函数]]==
3. 多线程读写的数据未加锁保护（临界区资源需要互斥访问）
4. 非法指针（如空指针异常或者非法地址访问）
5. 堆栈溢出