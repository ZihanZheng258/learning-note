# Pod

==**Pod** 是可以在 Kubernetes 中创建和管理的、最小的可部署的计算单元。==

**Pod**（就像在鲸鱼荚或者豌豆荚中）是一组（一个或多个） [容器](https://kubernetes.io/zh-cn/docs/concepts/containers/)； ==这些容器共享存储、网络、以及怎样运行这些容器的规约。== Pod 中的内容总是并置（colocated）的并且一同调度，在共享的上下文中运行。 ==Pod 所建模的是特定于应用的“逻辑主机”，其中包含一个或多个应用容器， 这些容器相对紧密地耦合在一起。== 在非云环境中，在相同的物理机或虚拟机上运行的应用类似于在同一逻辑主机上运行的云应用。

Pod 的共享上下文包括一组 Linux 名字空间、控制组（CGroup）和可能一些其他的隔离方面， 即用来隔离[容器](https://kubernetes.io/zh-cn/docs/concepts/containers/)的技术。 在 Pod 的上下文中，每个独立的应用可能会进一步实施隔离。

- **运行单个容器的 Pod**。"每个 Pod 一个容器"模型是最常见的 Kubernetes 用例； 在这种情况下，可以将 Pod 看作单个容器的包装器，并且 Kubernetes 直接管理 Pod，而不是容器。
    
- **运行多个协同工作的容器的 Pod**。 Pod 可以封装由紧密耦合且需要共享资源的[多个并置容器](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/#how-pods-manage-multiple-containers)组成的应用。 这些位于同一位置的容器构成一个内聚单元。

将多个并置、同管的容器组织到一个 Pod 中是一种相对高级的使用场景。 只有在一些场景中，容器之间紧密关联时你才应该使用这种模式。

### 用于管理 Pod 的工作负载资源[](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/#workload-resources-for-managing-pods)

通常你不需要直接创建 Pod，甚至单实例 Pod。相反，你会使用诸如 [Deployment](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/) 或 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/) 这类工作负载资源来创建 Pod。 如果 Pod 需要跟踪状态，可以考虑 [StatefulSet](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/statefulset/) 资源。==你很少在 Kubernetes 中直接创建一个个的 Pod，甚至是单实例（Singleton）的 Pod。 这是因为 Pod 被设计成了相对临时性的、用后即抛的一次性实体。==

==每个 Pod 都旨在运行给定应用程序的单个实例。如果希望横向扩展应用程序 （例如，运行多个实例以提供更多的资源），则应该使用多个 Pod==，每个实例使用一个 Pod。 在 Kubernetes 中，这通常被称为**副本（Replication）**。 通常使用一种工作负载资源及其[控制器](https://kubernetes.io/zh-cn/docs/concepts/architecture/controller/)来创建和管理一组 Pod 副本。

Pod 的名称必须是一个合法的 [DNS 子域](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#dns-subdomain-names)值， 但这可能对 Pod 的主机名产生意外的结果。为获得最佳兼容性，名称应遵循更严格的 [DNS 标签](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#dns-label-names)规则。