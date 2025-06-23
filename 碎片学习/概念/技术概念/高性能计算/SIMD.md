## **1.SIMD的历史与分类**

==SIMD(**Single Instruction Multiple Data**)即单指令流多数据流，是一种采用一个控制器来控制多个处理器，同时对一组数据（又称“数据向量”）中的每一个分别执行相同的操作从而实现空间上的并行性的技术==。简单来说就是一个指令能够同时处理多个数据。

![[Pasted image 20250620170207.png]]

如上图所示，==使用标量运算一次只能对一对数据执行乘法操作，而采用SIMD乘法指令，则一次可以对四对数据同时执行乘法操作。==

SIMD于20世纪70年代首次引用于[ILLIAC IV](https://zhida.zhihu.com/search?content_id=100424657&content_type=Article&match_order=1&q=ILLIAC+IV&zhida_source=entity)大规模并行计算机上。而大规模应用到消费级计算机则是在20实际90年代末。

1996年Intel推出了X86的[MMX](https://zhida.zhihu.com/search?content_id=100424657&content_type=Article&match_order=1&q=MMX&zhida_source=entity)(MultiMedia eXtension)指令集扩展，MMX定义了8个寄存器，称为MM0到MM7==，以及对这些寄存器进行操作的指令。每个寄存器为64位宽==，可用于以“压缩”格式保存64位整数或多个较小整数，==然后可以将单个指令一次应用于两个32位整数，四个16位整数或8个8位整数。==

intel在1999年又推出了全面覆盖MMX的SSE(**Streaming SIMD Extensions， 流式SIMD扩展**)指令集，==并将其应用到Pentium III系列处理器上，SSE添加了八个新的128位寄存器(XMM0至XMM7)，而后来的X86-64扩展又在原来的基础上添加了8个寄存器(XMM8至XMM15)。==SSE支持单个寄存器存储4个32位单精度浮点数，之后的SSE2则支持单个寄存器存储2个64位双精度浮点数，2个64位整数或4个32位整数或8个16位短整形。SSE2之后还有SSE3，SSE4以及AVX，AVX2等扩展指令集。

AVX引入了16个256位寄存器(YMM0至YMM15)，AVX的256位寄存器和SSE的128位寄存器存在着相互重叠的关系(XMM寄存器为YMM寄存器的低位)，所以最好不要混用AVX与SSE指令集，否在会导致[transition penalty](https://link.zhihu.com/?target=https%3A//software.intel.com/en-us/articles/avoiding-avx-sse-transition-penalties)(过渡处罚)，两种寄存器的关系如下图：

![[Pasted image 20250620171124.png]]

![[Pasted image 20250620171140.png]]