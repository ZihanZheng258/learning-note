一个 Kubernetes [控制平面](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-control-plane)组件， 嵌入了特定于云平台的控制逻辑。 ==云控制器管理器（Cloud Controller Manager）允许将你的集群连接到云提供商的 API 之上， 并将与该云平台交互的组件同与你的集群交互的组件分离开来。==

==`cloud-controller-manager` 仅运行特定于云平台的控制器。== 因此如果你在自己的环境中运行 Kubernetes，或者在本地计算机中运行学习环境， 所部署的集群不包含云控制器管理器。

与 `kube-controller-manager` 类似，`cloud-controller-manager` ==将若干逻辑上独立的控制回路组合到同一个可执行文件中，以同一进程的方式供你运行==。 你可以对其执行水平扩容（运行不止一个副本）以提升性能或者增强容错能力。

下面的控制器都包含对云平台驱动的依赖：

- Node 控制器：用于在节点终止响应后检查云平台以确定节点是否已被删除
- Route 控制器：用于在底层云基础架构中设置路由
- Service 控制器：用于创建、更新和删除云平台上的负载均衡器