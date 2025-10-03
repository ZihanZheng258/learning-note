OCI runtime 指的是实现了 ==**Open Container Initiative (OCI) Runtime Specification** 的任何软件==。它是容器技术栈中的一个关键中间层，负责**真正创建、启动、运行和管理容器**。

简单来说，**OCI runtime 就是容器的执行器**。

## OCI Runtime 的核心定位

要理解 OCI runtime，你需要了解容器技术栈的三个主要层次：

1. **顶层 (平台/引擎)**：例如 **Docker** 或 **Kubernetes** 的 **Containerd/CRI-O**。==它们提供用户接口、镜像管理、网络和存储的顶层逻辑。==
    
2. **中间层 (OCI Runtime)**：例如 **runC**。这是 **真正与 Linux 内核交互**，利用 **cgroups** 和 **namespaces** 来运行容器进程的工具。
    
3. **底层 (操作系统内核)**：**cgroups 和 namespaces**，提供容器隔离和资源限制的基础功能。

### OCI Runtime 的职责

OCI Runtime 遵循 OCI Runtime 规范，它接收一个 JSON 格式的 **配置包（Configuration Bundle）**，并执行以下任务：

- **创建容器环境**：根据配置（包括根文件系统路径、要运行的命令、以及 cgroups 和 namespaces 的设置），设置好所有的环境参数。
    
- **调用内核功能**：利用 Linux 内核的 **namespaces** 实现容器进程的隔离，利用 **cgroups** 实现资源限制。
    
- **启动进程**：在隔离的环境中启动用户指定的容器进程。
    
- **管理生命周期**：==提供标准接口，用于查询容器状态、发送信号（如停止），并删除容器。==

## 最知名的 OCI Runtime 示例：runC

目前最广泛使用的 OCI Runtime 是 **runC**。

- **runC** 是 Docker 公司捐赠给 OCI 社区的代码库，它是 **OCI Runtime 规范的参考实现**。
    
- **Docker Engine** 和现代的 **Containerd**、**CRI-O** 这些高级容器运行时（High-level Runtimes）在底层，==都是通过调用 **runC** 来执行实际的容器创建和运行操作的==。
    

### 为什么需要这个标准？

如果没有 OCI Runtime 规范，Kubernetes 或 Docker 就必须为每一种底层容器技术（==例如，不同的 Linux 发行版或未来的新技术）编写特定的集成代码。==

OCI 规范的出现，使得：

- **互操作性**：任何实现了 OCI 规范的容器运行时（如 runC、Kata Containers 等）都可以被任何上层容器管理工具（如 Containerd、CRI-O）调用。
    
- **可替换性**：用户可以在不改变上层应用的情况下，轻松替换底层的容器运行时。
    

**总结：** **OCI Runtime** 是容器技术栈中的关键执行者。它遵循一套标准（CRI）==，将上层管理工具的指令，转化为对操作系统内核的实际调用，从而实现容器的隔离和运行。==