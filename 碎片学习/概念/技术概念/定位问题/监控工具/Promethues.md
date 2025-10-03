[Prometheus](https://github.com/prometheus) 是最初在 [SoundCloud](https://soundcloud.com/) 上构建的开源系统监视和警报工具包。==自2012年成立以来，许多公司和组织都采用了Prometheus，该项目拥有非常活跃的开发人员和用户[社区](https://prometheus.io/community)。现在，它是一个独立的开源项目==，并且独立于任何公司进行维护。为了强调这一点并阐明项目的治理结构，Prometheus在2016年加入了 [Cloud Native Computing Foundation](https://cncf.io/)，这是继 [Kubernetes](https://kubernetes.io/) 之后的第二个托管项目。

有关Prometheus的详细说明，请参见 [media](https://hulining.gitbook.io/prometheus/introduction/media) 部分中的资源链接。

Prometheus 的主要特点是：

- 一种多维[数据模型](https://hulining.gitbook.io/prometheus/concepts/data_model)，==其中包含通过 metric 名称和键/值对标识的时间序列数据==
- 可利用各种维度的灵活的查询语句 [PromQL](https://hulining.gitbook.io/prometheus/prometheus/querying/basics)
- 不依赖分布式存储；单服务节点是自治的
- 时间序列通过 HTTP 拉取方式进行收集
- 支持通过中间网关[推送时间序列](https://hulining.gitbook.io/prometheus/instrumenting/pushing)
- 通过服务发现或静态配置发现目标
- 多种图形和仪表板支持模式

Prometheus 直接或通过 push gateway(主要用于短期作业)从已监测的作业中采集指标数据。它在本地存储所有已采集到的数据，并对这些数据运行规则，以汇总和记录新的时间序列，或产生告警。[Grafana](https://grafana.com/) 或其它 API 可用于可视化收集的数据

## Prometheus 适合什么场景

==Prometheus 非常适合记录纯数字时间序列的场景==。它即适合以机器为中心的监控，==也适合高度动态的面向服务的体系结构监控。在微服务中，它的优势在于多维数据收集和查询的支持。==

Prometheus 的设计旨在提高可靠性，在故障发生时能够快速定位问题。每个 Prometheus 服务器都是独立的，而不依赖于网络存储或其他远程服务。当其它基础设备发生故障时，您可以依靠它，并且无需昂贵的基础设备即可使用它

Prometheus 重视可靠性，即使在故障情况下，您始终可以查看有关系统的可用统计信息。==如果你需要 100% 的准确性，例如按请求计费，因为收集的数据可能不够详细和完整==，Prometheus 并不是一个好的选择。在这种场景下，您最好使用其他系统来收集和分析数据以进行计费，使用 Prometheus 进行其余的监视。