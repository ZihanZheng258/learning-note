**podman**（Pod Manager）是一个由 RedHat 公司推出的容器管理工具，它的定位就是 docker 的替代品，在使用上与 docker 的体验类似。==**podman** 源于 CRI-O 项目，可以直接访问 OCI 的实现（如 runC），流程比 docker 要短。==

![[Pasted image 20250928100107.png]]

二者主要的区别在于，**podman** 是一个开源的产品；而 docker 已经是商业化的产品。

**podman** 的开源代码，由 RedHat 的 OpenShift 项目维护。podman.io 上面的文档还不算很健全，作为普通开发者，将其当成 docker 去用，难度也不算很大。

虽然 RedHat 推行 **podman** 的方式，还有一些“象牙塔”、“学院派”，但由于 docker 自身的问题，从开源社区江湖地位的考虑，**podman** 有替代昔日 docker 地位的趋势，甚至可以说是大势所趋。

## _2 podman 和 Docker 的主要区别是什么？_

==dockers 在实现 CRI 的时候，它需要一个守护进程，其次需要以 root 运行，因此这也带来了安全隐患。==

**podman** 不需要守护程序，也不需要 root 用户运行，从逻辑架构上，比 docker 更加合理。

在 docker 的运行体系中，需要多个 daemon 才能调用到 OCI 的实现 RunC。

![[Pasted image 20250928101335.png]]

在容器管理的链路中，Docker Engine 的实现就是 dockerd daemon，它在 linux 中需要以 root 运行，dockerd 调用 containerd，containerd 调用 containerd-shim，然后才能调用 runC。顾名思义 shim 起的作用也就是“垫片”，避免父进程退出影响容器的运训。

**podman** 直接调用 OCI runtime（runC），通过 common 作为容器进程的管理工具，但不需要 dockerd 这种以 root 身份运行的守护进程。

在 **podman** 体系中，有个称之为 common 的守护进程，其运行路径通常是/usr/libexec/podman/conmon，它是各个容器进程的父进程，每个容器各有一个，common 的父则通常是 1 号进程。podman 中的 common 其实相当于 docker 体系中的 containerd-shim。

下图常用来描述 podman 与 docker 的区别
![[Pasted image 20250928101419.png]]

图中所体现的事情是，**podman** 不需要守护进程，而 dorker 需要守护进程。在这个图的示意中，dorcker 的 containerd-shim 与 podman 的 common 被归在 Container 一层。

## 3 _podman 的使用与 docker 有什么区别？_

**podman** 的定位也是与 docker 兼容，因此在使用上面尽量靠近 docker。在使用方面，可以分成两个方面来说，一是系统构建者的角度，二是使用者的角度。

在系统构建者方面，用 **podman** 的默认软件，与 docker 的区别不大，只是在进程模型、进程关系方面有所区别。如果习惯了 docker 几个关联进程的调试方法，在 **podman** 中则需要适应。可以通过 pstree 命令查看进程的树状结构。总体来看，**podman** 比 docker 要简单。由于 **podman** 比 docker 少了一层 daemon，因此重启的机制也就不同了。

在使用者方面，**podman** 与 docker 的命令基本兼容，都包括容器运行时（run/start/kill/ps/inspect），本地镜像（images/rmi/build）、镜像仓库（login/pull/push）等几个方面。因此 podman 的命令行工具与 docker 类似，比如构建镜像、启停容器等。甚至可以通过 alias docker=podman 可以进行替换。因此，即便使用了 podman，仍然可以使用 docker.io 作为镜像仓库，这也是兼容性最关键的部分。

下图表示 docker、podman 的二级命令，它们相当接近。

![[Pasted image 20250928101443.png]]
**podman** 相比 docker 也缺失了一些功能，比如不支持 windows，不支持 docker-compoese 编排工具。显然在 Kubernetes 或者 OpenShift 体系中，这些并不重要。

## _4 podman 相关的东西还有什么？_

**podman** 是 github 的 Containers 项目的一部分，这里面还包括几个相关的项目，它们都是用 go 语言组成的。

![[Pasted image 20250928101512.png]]

下面是 podman、buildah、skopeo 三个软件的图标。

![[Pasted image 20250928101529.png]]

**podman**、buildah、skopeo 组成了一个完整的容器工具体系：

- **podman** 项目对标的是 docker 命令的代替，官方说明是 _A tool for managing OCI containers and pods_
    
- buildah 项目实现的是 dockerfile 的脚本化执行，官方说明是 _A tool that facilitates building OCI images_
    
- skopeo 项目负责处理镜像相关的工作，比如检查、复制、签名，官方说明是 _Work with remote images registries - retrieving information, images, signing content_
    

buildah 完成对镜像的操作，类似 docker build，也可以进行 push 等操作；skopeo 完成对镜像仓库的操作，包括 cp、inspect、delete 等操作。它们的功能都比较纯粹，它们都是对 **podman** 功能的补充。

在 Centos 8 中，已经不适用 docker 作为默认的容器化工具，==替代品也就是使用 **podman**、buildah、skopeo。==