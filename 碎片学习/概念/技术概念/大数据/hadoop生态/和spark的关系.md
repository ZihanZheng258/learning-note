首先，==Apache Spark与Apache Hadoop两者都是大数据框架，但是各自存在的目的不尽相同。==Hadoop实质上更多是一个分布式数据基础设施，它将巨大的数据集分派到一个由普通计算机组成的集群中的多个节点进行存储，意味着我们不需要购买和维护昂贵的服务器硬件。

同时，Hadoop还会索引和跟踪这些数据、让大数据处理和分析效率达到前所未有的高度。==Spark则是一个用来对那些分布式存储的大数据进行处理的工具，它并不会进行分布式数据的存储。==

**2.数据处理方式不同**  
Spark因为其处理的方式不一样，会比[Hadoop MapRecue](https://zhida.zhihu.com/search?content_id=124175496&content_type=Article&match_order=1&q=Hadoop+MapRecue&zhida_source=entity)快上很多。==Hadoop MapRecue是分步对数据进行处理的==，先从集群中读取数据，进行一次处理，将结果写到集群，从集群中读取更新后的数据，再进行下一次的处理，将结果写到集群。

而反观Spark，==它会在内存中以接近“实时”的时间完成所有的数据分析，集群中读取数据，完成所有必须的分析处理，将结果写回集群，整个计算过程就完成了。==所以Spark的批处理速度比Hadoop MapRecue快近10倍，内存中的数据分析速度则快近100倍。

如果需要处理的数据和结果需求大部分情况下是静态的，而且你也不耐心等待批处理完成的话，Hadoop MapRecue的处理方式也是完成可以接受的。==但如果你需要对流数据进行分析，比如那些来自工厂的传感器收集回来的数据，又或者说你的应用是需要多重数据处理的，那你也许更应该使用Spark进行处理==

大部分机器学习算法都是需要多重数据处理的。此外，通常会用到Spark的应用场景有以下几个方面：实时的市场活动、在线产品推荐、网络安全分析、机器日志监控等。

**3.容灾处理**  
两者的空难恢复方式迥异，但是都不错。==因为Hadoop将每次处理后的数据写入到磁盘上，所以其天生就能很有弹性地对系统错误进行处理。==Spark的数据对象存储在分布于数据集群中的叫作[弹性分布式数据集](https://zhida.zhihu.com/search?content_id=124175496&content_type=Article&match_order=1&q=%E5%BC%B9%E6%80%A7%E5%88%86%E5%B8%83%E5%BC%8F%E6%95%B0%E6%8D%AE%E9%9B%86&zhida_source=entity)（RDD，Resilient Distributed Dataset）中。这些数据对象即可以放在内存中，也可以放在磁盘中，==所以RDD同样也可以提供完整的空难恢复功能。==

概念： **Spark**  
Spark是一个快速和通用的集群计算系统。特别：  

1. 快速 Spack具有支持循环数据流和内存计算的先进的==DAG执行引擎，所以比Hadoop MapRecue在内存计算上快100倍，在硬盘计算上快10倍。==
2. 易于使用 Spark提供了Java，Scala，Python和R等语言的高级API，可以用于快速开发相关语言应用。Spark提供了超过80个高级的操作，可以轻松构建并行应用程序。
3. 全面 Spark提供了[Spark SQL](https://zhida.zhihu.com/search?content_id=124175496&content_type=Article&match_order=1&q=Spark+SQL&zhida_source=entity)，机器学习的[MLlib](https://zhida.zhihu.com/search?content_id=124175496&content_type=Article&match_order=1&q=MLlib&zhida_source=entity)，进行图形处理的[GraphX](https://zhida.zhihu.com/search?content_id=124175496&content_type=Article&match_order=1&q=GraphX&zhida_source=entity)，以及[Spark Streaming](https://zhida.zhihu.com/search?content_id=124175496&content_type=Article&match_order=1&q=Spark+Streaming&zhida_source=entity)等库。你可以在同一应用程序无缝地合并这些库。
4. 到处运行 可以standalone cluster mode运行EC2、Hadoop YARN、或者Apache Mesos中。可以访问HDFS、Cassandra、HBase、Hive、Tachyon，以及任意的Hadoop数据源。