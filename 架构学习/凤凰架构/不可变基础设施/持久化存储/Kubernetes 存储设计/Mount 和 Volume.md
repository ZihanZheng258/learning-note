Mount 和 Volume 都是来源自操作系统的常用术语，Mount 是动词，表示将某个外部存储挂载到系统中，Volume 是名词，表示物理存储的逻辑抽象，目的是为物理存储提供有弹性的分割方式。容器源于对操作系统层的虚拟化，为了满足容器内生成数据的外部存储需求，也很自然地会将 Mount 和 Volume 的概念延拓至容器中。我们去了解容器存储的发展，不妨就以 Docker 的 Mount 操作为起始点。

目前，Docker 内建支持了三种挂载类型，分别是 Bind（`--mount type=bind`）、Volume（`--mount type=volume`）和 tmpfs（`--mount type=tmpfs`），如图 13-1 所示。其中 tmpfs 用于在内存中读写临时数据，与本节主要讨论的对象持久化存储并不相符，所以后面我们只着重关注 Bind 和 Volume 两种挂载类型。

![[Pasted image 20251016153546.png]]

Bind Mount 是 Docker 最早提供的（发布时就支持）挂载类型，作用是把宿主机的某个目录（或文件）挂载到容器的指定目录（或文件）下，譬如以下命令中参数`-v`表达的意思就是将外部的 HTML 文档挂到 Nginx 容器的默认网站根目录下：

``` sh
docker run -v /icyfenix/html:/usr/share/nginx/html nginx:latest
```

请注意，虽然命令中`-v`参数是`--volume`的缩写，但`-v`最初只是用来创建 Bind Mount 而不是创建 Volume Mount 的，这种迷惑的行为也并非 Docker 的本意，只是由于 Docker 刚发布时考虑得不够周全，随随便便就在参数中占用了“Volume”这个词，到后来真的需要扩展 Volume 的概念来支持 Volume Mount 时，前面的`-v`已经被用户广泛使用了，所以也就只得如此将就着继续用。从 Docker 17.06 版本开始，它在 Docker Swarm 中借用了`--mount`参数过来，这个参数默认创建的是 Volume Mount，可以通过明确的 type 子参数来指定另外两种挂载类型。上面命令可以等价于`--mount`版本如下形式：

```shell
docker run --mount type=bind,source=/icyfenix/html,destination=/usr/share/nginx/html nginx:latest
```

从 Bind Mount 到 Volume Mount，实质是容器发展过程中对存储抽象能力提升的外在表现。从“Bind”这个名字，以及 Bind Mount 的实际功能可以合理地推测，Docker 最初认为“Volume”就只是一种“外部宿主机的磁盘存储到内部容器存储的映射关系”，但后来眉头一皱发现事情并没有那么简单：存储的位置并不局限只在外部宿主机、存储的介质并不局限只是物理磁盘、存储的管理也并不局限只有映射关系。

譬如，Bind Mount 只能让容器与本地宿主机之间建立了某个目录的映射，如果想要在不同宿主机上的容器共享同一份存储，就必须先把共享存储挂载到每一台宿主机操作系统的某个目录下，然后才能逐个挂载到容器内使用，这种跨宿主机共享存储的场景如图 13-2 所示。

![[Pasted image 20251016153807.png]]

这种存储范围超越了宿主机的共享存储，配置过程却要涉及到大量与宿主机环境相关的操作，只能由管理员人工去完成，不仅烦琐，而且每台宿主机环境的差异导致还很难自动化。

又譬如即便只考虑单台宿主机的情况，基于可管理性的需求，Docker 也完全有支持 Volume Mount 的必要。Bind Mount 的设计里，Docker 只有容器的控制权，存放容器生产数据的主机目录是完全独立的，与 Docker 没有任何关系，既不受 Docker 保护，也不受 Docker 管理。数据很容易被其他进程访问到，甚至是被修改和删除。如果用户想对挂载的目录进行备份、迁移等管理运维操作，也只能在 Docker 之外靠管理员人工进行，这都增加了数据安全与操作意外的风险。因此，Docker 希望能有一种抽象的资源来代表在宿主机或网络中存储的区域，以便让 Docker 能管理这些资源，由此就很自然地联想到了操作系统里 Volume 的概念。

最后，提出 Volume 最核心的一个目的是为了提升 Docker 对不同存储介质的支撑能力，这同时也是为了减轻 Docker 本身的工作量。存储并不是仅有挂载在宿主机上的物理存储这一种介质，云计算时代，网络存储渐成数据中心的主流选择，不同的网络存储有各自的协议和交互接口，而且并非所有存储系统都适合先挂载到操作系统，然后再挂载到容器的，如果 Docker 想要越过操作系统去支持挂载某种存储系统，首先必须要知道该如何访问它，然后才能将容器中的读写操作自动转移到该位置。Docker 把解决如何访问存储的功能模块称为存储驱动（Storage Driver）。通过`docker info`命令，你能查看到当前 Docker 所支持的存储驱动。虽然 Docker 已经内置了市面上主流的 OverlayFS 驱动，譬如 Overlay、Overlay2、AUFS、BTRFS、ZFS，等等，但面对云计算的快速迭代，仅靠 Docker 自己来支持全部云计算厂商的存储系统是完全不现实的，为此，Docker 提出了与 Storage Driver 相对应的 Volume Driver（卷驱动）的概念。用户可以通过`docker plugin install`命令安装[外部的卷驱动](https://docs.docker.com/engine/extend/legacy_plugins/)，并在创建 Volume 时指定一个与其存储系统相匹配的卷驱动，譬如希望数据存储在 AWS Elastic Block Store 上，就找一个 AWS EBS 的驱动，如果想存储在 Azure File Storage 上，也是找一个对应的 Azure File Storage 驱动即可。如果创建 Volume 时不指定卷驱动，那默认就是 local 类型，在 Volume 中存放的数据会存储在宿主机的`/var/lib/docker/volumes/`目录之中。