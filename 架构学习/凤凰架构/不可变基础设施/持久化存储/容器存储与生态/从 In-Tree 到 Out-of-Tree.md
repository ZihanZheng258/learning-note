Kubernetes 原本曾内置了相当多的 In-Tree 的存储驱动，时间上甚至还早于 Docker 宣布支持卷驱动功能，这种策略使得 Kubernetes 能够在云存储提供商发布官方驱动之前就将其纳入到支持范围中，同时减轻了管理员维护的工作量，为它在诞生初期快速占领市场做出了一定的贡献。但是，这种策略也让 Kubernetes 丧失了随时添加或修改存储驱动的灵活性，只能在更新大版本时才能加入或者修改驱动，导致云存储提供商被迫要与 Kubernetes 的发版节奏保持一致。此外，还涉及到第三方存储代码混杂在 Kubernetes 二进制文件中可能引起的可靠性及安全性问题。因此，当 Kubernetes 成为市场主流以后——准确的时间点是从 1.14 版本开始，Kubernetes 启动了 In-Tree 存储驱动的 CSI 外置迁移工作，按照计划，在 1.21 到 1.22 版本（大约在 2021 年中期）时，Kubernetes 中主要的存储驱动，如 AWS EBS、GCE PD、vSphere 等都会迁移至符合 CSI 规范的 Out-of-Tree 实现，不再提供 In-Tree 的支持。这种做法在设计上无疑是正确的，然而，这又面临了此前提过的该如何兼容旧功能的策略问题，譬如下面 YAML 定义了一个 Pod：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-example
spec:
  containers:
  - name: nginx
    image: nginx:latest
    volumeMounts:
    - name: html-pages-volume
      mountPath: /usr/share/nginx/html
    - name: config-volume
      mountPath: /etc/nginx
    volumes:
    - name: html-pages-volume
      hostPath:                 # 来自本地的存储
        path: /srv/nginx/html
        type: Directory
    - name: config-volume
      awsElasticBlockStore:     # 来自AWS ESB的存储
        volumeID: vol-0b39e0b08745caef4
        fsType: ext4
```

其中用到了类型为 hostPath 的 Volume，这相当于 Docker 中驱动类型为 local 的 Volume，不需要专门的驱动；而类型为 awsElasticBlockStore 的 Volume，从名字上就能看出是指存储驱动为 AWS EBS 的 Volume，当 CSI 迁移完成，awsElasticBlockStore 从 In-Tree 卷驱动中移除掉之后，它就应该按照 CSI 的写法改写成如下形式：

```
    - name: config-volume
      csi:
        driver: ebs.csi.aws.com
        volumeAttributes:
          - volumeID: vol-0b39e0b08745caef4
          - fsType: ext4
```

这样的要求有悖于升级版本不应影响还在大范围使用的已有功能这条原则，所以 Kubernetes 1.17 中又提出了称为[CSIMigration](https://kubernetes.io/blog/2019/12/09/kubernetes-1-17-feature-csi-migration-beta/)的解决方案，让 Out-of-Tree 的驱动能够自动伪装成 In-Tree 的接口来提供服务。 笔者专门花这两段来介绍 Volume 的 CSI 迁移，倒不是由于它算是多么重要的特性，而是这种兼容性设计本身就是 Kubernetes 设计理念的一个缩影，在 Kubernetes 的代码与功能中随处可见。好的设计需要权衡多个方面的利益，很多时候都得顾及现实的影响，要求设计向现实妥协，而不能仅仅考虑理论最优的方案。