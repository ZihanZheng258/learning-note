介绍 DNS 域名解析时，笔者曾提到翻译域名无须像查电话本一样刻板地一对一翻译，==根据来访机器、网络链路、服务内容等各种信息，可以玩出很多花样==，内容分发网络将用户请求路由到它的资源服务器上就是依靠 DNS 服务器来实现的。根据我们对 DNS 域名解析的了解，一次没有内容分发网络参与的用户访问，其解析过程如图 4-5 所示。

![[Pasted image 20250613105920.png]]

有内容分发网络介入会发生什么变化呢？我们不妨先来看一段对网站`icyfenix.cn`进行 DNS 查询的真实应答记录，==这个网站就是通过国内的内容分发网络对位于[GitHub Pages](https://pages.github.com/)上的静态页面进行加速的。通过 dig 或者 host 命令，就能很方便地得到 DNS 服务器的返回结果==（结果中头 4 个 IP 的城市地址是笔者手工加入的，后面的其他记录就不一个一个查了），如下所示：

根据以上解析信息，DNS 服务为`icyfenix.cn`的查询结果先返回了一个[CNAME 记录](https://en.wikipedia.org/wiki/CNAME_record)（`icyfenix.cn.cdn.dnsv1.com.`），递归查询该 CNAME 时候，返回了另一个看起来更奇怪的 CNAME（`4yi4q4z6.dispatch.spcdntip.com.`）。继续查询后，这个 CNAME 返回了十几个位于全国不同地区的 A 记录，很明显，那些 A 记录就是分布在全国各地、存有本站缓存的 CDN 节点。CDN 路由解析的具体工作过程是：

1. 架设好“`icyfenix.cn`”的服务器后，将服务器的 IP 地址在你的 CDN 服务商上注册为“源站”，注册后你会得到一个 CNAME，即本例中的“`icyfenix.cn.cdn.dnsv1.com.`”。
    
2. 将得到的 CNAME 在你购买域名的 DNS 服务商上注册为一条 CNAME 记录。
    
3. 当第一位用户来访你的站点时，==将首先发生一次未命中缓存的 DNS 查询，域名服务商解析出 CNAME 后，返回给本地 DNS，至此之后链路解析的主导权就开始由内容分发网络的调度服务接管了。==
    
4. 本地 DNS 查询 CNAME 时，由于能解析该 CNAME 的权威服务器只有 CDN 服务商所架设的权威 DNS，==这个 DNS 服务将根据一定的均衡策略和参数，如拓扑结构、容量、时延等，在全国各地能提供服务的 CDN 缓存节点中挑选一个最适合的，将它的 IP 代替源站的 IP 地址，返回给本地 DNS==。
    
5. 浏览器从本地 DNS 拿到 IP 地址，将该 IP 当作源站服务器来进行访问，此时该 IP 的 CDN 节点上可能有，也可能没有缓存过源站的资源，这点将在稍后“[内容分发](https://icyfenix.cn/architect-perspective/general-architecture/diversion-system/cdn.html#%E5%86%85%E5%AE%B9%E5%88%86%E5%8F%91)”小节讨论。
    
6. 经过内容分发后的 CDN 节点，就有能力代替源站向用户提供所请求的资源。

![[Pasted image 20250613110656.png]]