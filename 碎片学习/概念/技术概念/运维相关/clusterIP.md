**ClusterIP** 是 Kubernetes 中 **Service（服务）** 的默认类型，它是保障 Kubernetes 集群内部通信稳定性的核心机制。

简单来说，==**ClusterIP** 为一组后端 Pod 提供了一个**稳定的、内部可访问的虚拟 IP 地址**。==

### 核心作用与特点

1. **内部稳定接入点 (Virtual IP):**
    
    - Kubernetes 会从服务网段中分配一个仅在集群内部可用的虚拟 IP 地址，这就是 **ClusterIP**。
        
    - 这个 IP 地址是**稳定不变**的，即使后端的 Pod 宕机、重建或扩缩容，这个 ClusterIP 仍然有效。
        
    - 集群内的其他 Pods 或组件，==可以通过这个固定的 ClusterIP 和端口来访问后端服务，而不必关心后端 Pod 的实际 IP 变动。==
        
2. **负载均衡 (Load Balancing):**
    
    - ClusterIP 不仅仅是一个 IP 地址，它还提供了 L4 层的负载均衡能力。
        
    - 当流量发送到 ClusterIP 时，Kubernetes 会通过 **kube-proxy** 组件（在每个节点上运行）配置的规则（通常是 `iptables` 或 `ipvs`），将请求路由并分发到后端的某一个健康 Pod 上。
        
3. **服务发现 (Service Discovery):**
    
    - 当创建 ClusterIP Service 时，Kubernetes 的 **CoreDNS** 会自动创建一个对应的 DNS 记录。
        
    - 集群内的 Pods 可以通过服务的名称（例如 `my-app-service`）而不是 IP 地址来访问它，例如 `http://my-app-service:8080`，这大大简化了应用间的通信。
        
4. **集群内部限定 (Internal Only):**
    
    - **ClusterIP 仅在 Kubernetes 集群内部网络中可达。**
        
    - 外部世界（即集群外部的网络）无法直接通过这个 ClusterIP 访问您的服务。如果需要将服务暴露给外部，您需要使用其他 Service 类型，如 `NodePort`、`LoadBalancer` 或 `Ingress`。
        

### 总结

|特性|ClusterIP|
|---|---|
|**访问范围**|仅限集群内部|
|**IP 地址**|稳定的虚拟 IP (ClusterIP)|
|**负载均衡**|默认提供 L4 负载均衡|
|**主要用途**|集群内服务间的相互调用|
|**默认类型**|是 Kubernetes Service 的默认类型|

导出到 Google 表格

**一句话：ClusterIP 是 Kubernetes 集群内部的“门牌号”，它使得集群内的应用可以稳定地互相发现和访问。**