介绍Disruptor之前，我们先来看一看常用的线程安全的内置队列有什么问题。Java的内置队列如下表所示

|队列|有界性|锁|数据结构|
|---|---|---|---|
|ArrayBlockingQueue|bounded|加锁|arraylist|
|LinkedBlockingQueue|optionally-bounded|加锁|linkedlist|
|ConcurrentLinkedQueue|unbounded|无锁|linkedlist|
|LinkedTransferQueue|unbounded|无锁|linkedlist|
|PriorityBlockingQueue|unbounded|加锁|heap|
|DelayQueue|unbounded|加锁|heap|

队列的底层一般分成三种：==数组、链表和堆。其中，堆一般情况下是为了实现带有优先级特性的队列，暂且不考虑。==

我们就从数组和链表两种数据结构来看，基于数组线程安全的队列，比较典型的是ArrayBlockingQueue，它主要通过加锁的方式来保证线程安全；基于链表的线程安全队列分成LinkedBlockingQueue和ConcurrentLinkedQueue两大类，==前者也通过锁的方式来实现线程安全，而后者以及上面表格中的LinkedTransferQueue都是通过原子变量compare and swap（以下简称“CAS”）这种不加锁的方式来实现的。==

通过不加锁的方式实现的队列都是无界的（无法保证队列的长度在确定的范围内）；而加锁的方式，可以实现有界队列。==在稳定性要求特别高的系统中，为了防止生产者速度过快，导致内存溢出，只能选择有界队列；==同时，为了减少Java的垃圾回收对系统性能的影响，会尽量选择array/heap格式的数据结构。这样筛选下来，符合条件的队列就只有ArrayBlockingQueue。

## ArrayBlockingQueue的问题

ArrayBlockingQueue在实际使用过程中，会因为加锁和伪共享等出现严重的性能问题，我们下面来分析一下。

### 加锁

现实编程过程中，加锁通常会严重地影响性能。线程会因为竞争不到锁而被挂起，等锁被释放的时候，线程又会被恢复，这个过程中存在着很大的开销，并且通常会有较长时间的中断，因为当一个线程正在等待锁时，它不能做任何其他事情。==如果一个线程在持有锁的情况下被延迟执行，例如发生了缺页错误、调度延迟或者其它类似情况，那么所有需要这个锁的线程都无法执行下去。==如果被阻塞线程的优先级较高，而持有锁的线程优先级较低，就会发生优先级反转。

Disruptor论文中讲述了一个实验：

- 这个测试程序调用了一个函数，该函数会对一个64位的计数器循环自增5亿次。
- 机器环境：2.4G 6核
- 运算： 64位的计数器累加5亿次

|Method | Time (ms) | |— | —| |Single thread | 300| |Single thread with CAS | 5,700| |Single thread with lock | 10,000| |Single thread with volatile write | 4,700| |Two threads with CAS | 30,000| |Two threads with lock | 224,000|

CAS操作比单线程无锁慢了1个数量级；==有锁且多线程并发的情况下，速度比单线程无锁慢3个数量级。可见无锁速度最快。==

单线程情况下，不加锁的性能 > CAS操作的性能 > 加锁的性能。

在多线程情况下，为了保证线程安全，必须使用CAS或锁，这种情况下，CAS的性能超过锁的性能，前者大约是后者的8倍。

综上可知，加锁的性能是最差的。

在高度竞争的情况下，==锁的性能将超过原子变量的性能，但是更真实的竞争情况下，原子变量的性能将超过锁的性能。==同时原子变量不会有死锁等活跃性问题。