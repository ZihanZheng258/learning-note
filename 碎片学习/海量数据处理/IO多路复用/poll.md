poll 和 select 几乎没有区别。poll 在用户态通过**数组**方式**传递**文件描述符，在内核会转为**链表**方式**存储**，没有最大数量的限制 (感谢 [@LydiaCai1203](https://github.com/LydiaCai1203)、[@kingcanfish](https://github.com/kingcanfish) 指出)。

其中 `fds` 是一个 `pollfd` 结构体类型的数组，调用 `poll()` 时必须通过 `nfds` 指出数组 `fds` 的大小，即文件描述符的数量。详细描述见 [manpage - poll(2)](https://man7.org/linux/man-pages/man2/poll.2.html)。

从性能开销上看，poll 和 select 的差别不大