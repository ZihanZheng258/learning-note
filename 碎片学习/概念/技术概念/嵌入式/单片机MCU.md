单片机的全称是单片微型计算机简称单片机(MCU)，是典型的[嵌入式微控制器](https://zhida.zhihu.com/search?content_id=206924711&content_type=Answer&match_order=1&q=%E5%B5%8C%E5%85%A5%E5%BC%8F%E5%BE%AE%E6%8E%A7%E5%88%B6%E5%99%A8&zhida_source=entity)。==单片机是一种集成电路芯片，是采用超大规模集成电路技术把具有数据处理能力的中央处理器CPU[随机存储器](https://zhida.zhihu.com/search?content_id=206924711&content_type=Answer&match_order=1&q=%E9%9A%8F%E6%9C%BA%E5%AD%98%E5%82%A8%E5%99%A8&zhida_source=entity)RAM、[只读存储器](https://zhida.zhihu.com/search?content_id=206924711&content_type=Answer&match_order=1&q=%E5%8F%AA%E8%AF%BB%E5%AD%98%E5%82%A8%E5%99%A8&zhida_source=entity)ROM、多种[I/O口](https://zhida.zhihu.com/search?content_id=206924711&content_type=Answer&match_order=1&q=I%2FO%E5%8F%A3&zhida_source=entity)和[中断系统](https://zhida.zhihu.com/search?content_id=206924711&content_type=Answer&match_order=1&q=%E4%B8%AD%E6%96%AD%E7%B3%BB%E7%BB%9F&zhida_source=entity)、[定时器/计时器](https://zhida.zhihu.com/search?content_id=206924711&content_type=Answer&match_order=1&q=%E5%AE%9A%E6%97%B6%E5%99%A8%2F%E8%AE%A1%E6%97%B6%E5%99%A8&zhida_source=entity)等功能(可能还包括显示驱动电路、脉宽调制电路、模拟多路转换器、A/D转换器等电路)集成到一块硅片上构成的一个小而完善的微型计算机系统。==

单片机又称单片微控制器,是因为它早被用在工业控制领域。==它不是完成某一个逻辑功能的芯片,而是把一个计算机系统集成到一个芯片上。相当于一个微型的计算机，和计算机相比，单片机只缺少了I/O设备==。概括的讲：一块芯片就成了一台计算机。它的体积小、质量轻、价格便宜、为学习、应用和开发提供了便利条件。同时，学习使用单片机是了解计算机原理与结构的佳选择。

**简单地说，单片机就是一个小计算机系统。**

单片机比专用处理器适合应用于嵌入式系统，因此它得到了多的应用。事实上单片机是世界上数量多的计算机，现代人类生活中所用的几乎每件电子和机械产品中都会集成有单片机。手机、电话、计算器、家用电器、电子玩具、掌上电脑以及鼠标等电脑配件中都配有1-2部单片机。==而个人电脑中也会有为数不少的单片机在工作。==

==单片机是什么?简而言之就是一个小计算机系统，单片机是指一个集成在一块芯片上的完整计算机系统==，而且单片机的应用非常广泛，现代人类生活中所用的几乎每件电子和机械产品中都会集成有单片机。

“单片机”其实是一种古老的叫法。在那个年代半导体工艺还在起步阶段，集成能力很差，往往是CPU一个芯片，SRAM一个芯片，Flash一个芯片，需要中断的话又得有个专门处理中断的芯片，所以一个完整可用的计算机系统是很多个芯片（Chip）做在一个PCB板上构成的。不同的功能无法做进一个芯片（Chip），所以会有多片机。现在半导体技术早已非常发达，所以不存在多片机。但是，“单片机”的叫法却一直延用至今。

从软件上，行业里经常把芯片中不==带MMU（memory management unit，内存管理单元）从而不支持虚拟地址，只能裸奔或运行RTOS（[实时操作系统](https://zhida.zhihu.com/search?content_id=426434910&content_type=Answer&match_order=1&q=%E5%AE%9E%E6%97%B6%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F&zhida_source=entity)，例如ucos、华为LiteOS、RT-Thread、freertos等）的system，==叫做单片机（如STM32、NXP LPC系列、NXP imxRT1052系列等）。

  同时，把芯片自带MMU可以支持虚拟地址，能够跑Linux、Vxworks、WinCE、Android这样的“高级”操作系统的system，叫做嵌入式。
  
在某些时候，单片机本身已经足够强大，可以作为嵌入式系统使用。它的成本更低，开发和维护的难度相对较小，尤其是针对一些针对性更强的应用。而嵌入式系统理论上性能更强，应用更广泛，但复杂度高，开发难度大。