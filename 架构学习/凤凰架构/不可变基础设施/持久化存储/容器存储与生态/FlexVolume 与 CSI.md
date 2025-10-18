
Kubernetes 目前同时支持[FlexVolume](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md)与[CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)（Container Storage Interface）两套独立的存储扩展机制。FlexVolume 是 Kubernetes 很早期版本（1.2 版开始提供，1.8 版达到 GA 状态）就开始支持的扩展机制，它是只针对 Kubernetes 的私有的存储扩展，目前已经处于冻结状态，可以正常使用但不再发展新功能了。CSI 则是从 Kubernetes 1.9 开始加入（1.13 版本达到 GA 状态）的扩展机制，如同之前介绍过的 CRI 和 CNI 那样，==CSI 是公开的技术规范，任何容器运行时、容器编排引擎只要愿意支持，都可以使用 CSI 规范去扩展自己的存储能力，这是目前 Kubernetes 重点发展的扩展机制。==

由于是专门为 Kubernetes 量身订造的，所以 FlexVolume 的实现逻辑与上一节介绍的 Kubernetes 存储架构高度一致。FlexVolume 驱动其实就是一个实现了 Attach、Detach、Mount、Unmount 操作的可执行文件（甚至可以仅仅是个 Shell 脚本）而已，该可执行文件应该存放在集群每个节点的`/usr/libexec/kubernetes/kubelet-plugins/volume/exec`目录里，其工作过程也就是当 AD 控制器和 Volume 管理器需要进行 Attach、Detach、Mount、Unmount 操作时自动调用它的对应方法接口，如图 13-7 所示。

![[Pasted image 20251016155139.png]]

如果仅仅考虑支持最基本的 Static Provisioning，那实现一个 FlexVolume Driver 确实是非常简单的。然而也是由于 FlexVolume 过于简单了，导致它应用起来会有诸多不便之处，譬如：

- FlexVolume 并不是全功能的驱动：FlexVolume 不包含 Provision 和 Delete 操作，也就无法直接用于 Dynamic Provisioning，除非你愿意再单独编写一个 External Provisioner。
- FlexVolume 部署维护都相对繁琐：FlexVolume 是独立于 Kubernetes 的可执行文件，当集群节点增加时，需要由管理员在新节点上部署 FlexVolume Driver，有经验的系统管理员通常会专门编写一个 DaemonSet 来代替人工来完成这项任务。
- FlexVolume 实现复杂交互也相对繁琐：FlexVolume 的每一次操作，都是对插件可执行文件的一次独立调用，这种插件实现方式在各种操作需要相互通讯时会很别扭。譬如你希望在执行 Mount 操作的时候，生成一些额外的状态信息，这些信息在后面执行 Unmount 操作时去使用，那就只能把信息记录在某个约定好的临时文件中，对于一个面向生产的容器编排系统，这样的做法实在是过于简陋了。

相比起 FlexVolume 的种种不足，==CSI 可算是一个十分完善的存储扩展规范，这里“十分完善”可不是客套话==，根据 GitHub 的自动代码行统计，FlexVolume 的规范文档仅有 155 行，而 CSI 则长达 2704 行。总体上看，==CSI 规范可以分为需要容器系统去实现的组件，以及需要存储提供商去实现的组件两大部分==。前者包括了存储整体架构、Volume 的生命周期模型、驱动注册、Volume 创建、挂载、扩容、快照、度量等内容，这些 Kubernetes 都已经完整地实现了，大体上包括以下几个组件：

- [Driver Register](https://github.com/kubernetes-csi/driver-registrar)：负责注册第三方插件，CSI 0.3 版本之后已经处于 Deprecated 状态，将会被[Node Driver Register](https://kubernetes-csi.github.io/docs/node-driver-registrar.html)所取代。
- [External Provisioner](https://github.com/kubernetes-csi/external-provisioner)：调用第三方插件的接口来完成数据卷的创建与删除功能。
- [External Attacher](https://github.com/kubernetes-csi/external-attacher)：调用第三方插件的接口来完成数据卷的挂载和操作。
- [External Resizer](https://github.com/kubernetes-csi/external-resizer)：调用第三方插件的接口来完成数据卷的扩容操作。
- [External Snapshotter](https://github.com/kubernetes-csi/external-snapshotter)：调用第三方插件的接口来完成快照的创建和删除。
- [External Health Monitor](https://github.com/kubernetes-csi/external-health-monitor)：调用第三方插件的接口来提供度量监控数据。

需要存储提供商去实现的组件才是 CSI 的主体部分，即前文中多次提到的“第三方插件”。这部分着重定义了外部存储挂载到容器过程中所涉及操作的抽象接口和具体的通讯方式，主要包括以下三个 gRPC 接口：

- **CSI Identity 接口**：用于描述插件的基本信息，譬如插件版本号、插件所支持的 CSI 规范版本、插件是否支持存储卷创建、删除功能、是否支持存储卷挂载功能，等等。此外 Identity 接口还用于检查插件的健康状态，开发者可以通过实现 Probe 接口对外提供存储的健康度量信息。

- **CSI Controller 接口**：用于从存储系统的角度对存储资源进行管理，譬如准备和移除存储（Provision、Delete 操作）、附加与分离存储（Attach、Detach 操作）、对存储进行快照，==等等。存储插件并不一定要实现这个接口的所有方法，对于存储本身就不支持的功能，可以在 CSI Identity 接口中声明为不提供。==

- **CSI Node 接口**：用于从集群节点的角度对存储资源进行操作，譬如存储卷的分区和格式化、将存储卷挂载到指定目录上、或者将存储卷从指定目录上卸载，等等。

![[Pasted image 20251016155250.png]]

与 FlexVolume 以单独的可执行程序的存在形式不同，CSI 插件本身便是由一组标准的 Kubernetes 资源所构成，==CSI Controller 接口是一个以 StatefulSet 方式部署的 gRPC 服务，CSI Node 接口则是基于 DaemonSet 方式部署的 gRPC 服务==。这意味着虽然 CSI 实现起来要比 FlexVolume 复杂得多，但是却很容易安装——如同安装 CNI 插件及其它应用那样，直接载入 Manifest 文件即可，也不会遇到 FlexVolume 那样需要人工运维，或者自己编写 DaemonSet 来维护集群节点变更的问题。此外，通过 gRPC 协议传递参数比通过命令行参数传递参数更加严谨，灵活和可靠，最起码不会出现多个接口之间协作只能写临时文件这样的尴尬状况。