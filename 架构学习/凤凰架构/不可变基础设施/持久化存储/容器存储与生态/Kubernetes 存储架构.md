正式开始讲解 Kubernetes 的 In-Tree、Out-of-Tree 存储插件前，我们有必要先去了解一点 Kubernetes 存储架构的知识，大体上弄清一个真实的存储系统是如何接入到新创建的 Pod 中，成为可以读写访问的 Volume 的；以及当 Pod 被销毁时，Volume 如何被回收，回归到存储系统之中的。==Kubernetes 参考了传统操作系统接入或移除新存储设备做法，把接入或移除外部存储这件事情分解为以下三种操作：==

- 首先，决定应**准备**（Provision）何种存储，==Provision 可类比为给操作系统扩容而购买了新的存储设备==。这步确定了接入存储的来源、容量、性能以及其他技术参数，它的逆操作是**移除**（Delete）存储。

- 然后，将准备好的存储**附加**（Attach）到系统中，==Attach 可类比为将存储设备接入操作系统，此时尽管设备还不能使用，但你已经可以用操作系统的`fdisk -l`命令查看到设备。==这步确定了存储的设备名称、驱动方式等面向系统一侧的信息，它的逆操作是**分离**（Detach）存储设备。
- 
- 最后，将附加好的存储**挂载**（Mount）到系统中，==Mount 可类比为将设备挂载到系统的指定位置，也就是操作系统中`mount`命令的作用==。这步确定了存储的访问目录、文件系统格式等面向应用一侧的信息，它的逆操作是**卸载**（Unmount）存储设备。

以上提到的 Provision、Delete、Attach、Detach、Mount、Unmount 六种操作，并不是直接由 Kubernetes 来实现，实际行为均是在存储插件中完成的，它们会分别被 Kubernetes 通过两个控制器及一个管理器来进行调用，这些控制器、管理器的作用分别是：

- **PV 控制器**（PersistentVolume Controller）：“[以容器构建系统](https://icyfenix.cn/immutable-infrastructure/container/container-build-system.html#%E9%9F%A7%E6%80%A7%E4%B8%8E%E5%BC%B9%E6%80%A7)”一节中介绍过，Kubernetes 里所有的控制器都遵循着相同的工作模式——让实际状态尽可能接近期望状态。==PV 控制器的期望状态有两个，分别是“所有未绑定的 PersistentVolume 都能处于可用状态”以及“所有处于等待状态的 PersistentVolumeClaim 都能配对到与之绑定的 PersistentVolume”==，它内部也有两个相对独立的核心逻辑（ClaimWorker 和 VolumeWorker）来分别跟踪这两种期望状态，==可以简单地理解为 PV 控制器实现了 PersistentVolume 和 PersistentVolumeClaim 的生命周期管理职能，在这个过程中，会根据需要调用存储驱动插件的 Provision/Delete 操作。==

- **AD 控制器**（Attach/Detach Controller）：AD 控制器的期望状态是“==所有被调度到准备新创建 Pod 的节点，都附加好了要使用的存储；当 Pod 被销毁后，原本运行 Pod 的节点都分离了不再被使用的存储”==，如果实际状态不符合该期望，会根据需要调用存储驱动插件的 Attach/Detach 操作。
- 
- **Volume 管理器**（Volume Manager）：Volume 管理器实际上是 kubelet 的一部分，是 kubelet 中众多管理器的其中一个，==它主要是用来支持本节点中 Volume 执行 Attach/Detach/Mount/Unmount 操作==。你可能注意到这里不仅有 Mount/Unmount 操作，也出现了 Attach/Detach 操作，这是历史原因导致的，==由于最初版本的 Kubernetes 中并没有 AD 控制器，Attach/Detach 的职责也在 kubelet 中完成==。现在 kubelet 默认情况下已经不再会执行 Attach/Detach 了，但有少量旧程序已经依赖了由 kubelet 来实现 Attach/Detach 的内部逻辑，所以 kubelet 不得不设计一个`--enable-controller-attach-detach`参数，如果将其设置为`false`的话就会重新回到旧的兼容模式上，由 kubelet 代替 AD 控制器来完成 Attach/Detach。

![[Pasted image 20251016155048.png]]
后端的真实存储依次经过 Provision、Attach、Mount 操作之后，就形成了可以在容器中挂载的 Volume，当存储的生命周期完结，依次经过 Unmount、Detach、Delete 操作之后，Volume 便能够被存储系统回收。==对于某些存储来说，其中有一些操作可能是无效的，譬如 NFS，实际使用并不需要 Attach，此时存储插件只需将 Attach 实现为空操作即可。==

