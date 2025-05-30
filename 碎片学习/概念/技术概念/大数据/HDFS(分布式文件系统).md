Hadoop 分布式文件系统 (HDFS) 是一种管理大型数据集的文件系统，可在商品硬件上运行。==HDFS 是 Hadoop 最常用的数据存储系统，可用于将单个 Apache Hadoop 集群扩展到数百甚至数千个节点。== 由于 HDFS 能以高吞吐量有效管理大数据，因此可用作[数据管道](https://www.ibm.com/cn-zh/topics/data-pipeline) ，非常适合支持复杂的[数据分析](https://www.ibm.com/cn-zh/topics/big-data-analytics)。

由于一个 HDFS 实例可能由数千台服务器组成，因此，至少有一台服务器始终有可能发生故障。HDFS 可检测故障并自动快速恢复。==跨多个节点制作多份[数据复制](https://www.ibm.com/cn-zh/topics/data-replication)副本有助于防止数据丢失。==HDFS 将至少一个副本保留在与所有其他副本不同的机架上。跨节点的大型集群中的[数据存储](https://www.ibm.com/cn-zh/topics/data-storage)提高了可靠性。此外，HDFS 还可以拍摄存储快照来保存时间点 (PIT) 信息。

