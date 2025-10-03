[Jaeger](https://www.jaegertracing.io/) 是一个分布式追踪系统。Jaeger的灵感来自 [Dapper](https://research.google.com/pubs/pub36356.html) 和 [OpenZipkin](https://zipkin.io/)，是一个由 Uber 创建并捐赠给 [云原生计算基金会（CNCF）](https://cncf.io/) 的分布式跟踪平台。它可以用于监控基于微服务的分布式系统：

- 分布式上下文传递
- 分布式事务监听
- 根因分析
- 服务依赖性分析
- 性能/延迟优化

Jaeger后端设计为无单点故障，并可根据业务需求进行扩展。例如，通常 Uber 的任何一个 Jaeger 实例每天都要处理数十亿个 span。