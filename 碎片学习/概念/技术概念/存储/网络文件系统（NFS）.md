网络文件系统（NFS）==协议由 Sun Microsystems 于 1984 年创建，是基于 Unix 的系统的有状态文件共享协议。此后，NFS 经历了几次更新==。最新版本是 NFS 版本 4（NFSv4），它是由互联网工程任务组的一个工作小组开发的。NFS 在 Linux 用户中仍然很受欢迎。

- [NFS](https://zhida.zhihu.com/search?content_id=187771378&content_type=Article&match_order=1&q=NFS&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDgyOTc5OTIsInEiOiJORlMiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxODc3NzEzNzgsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.16axIKyscZLU03pP4FuCznxaYRM_oP-KpzjNtdVsciQ&zhida_source=entity)（Network File System）即网络文件系统，是FreeBSD支持的文件系统中的一种，它允许网络中的计算机之间通过TCP/IP网络共享资源
- 在NFS的应用中，==本地NFS的客户端应用可以透明地读写位于远端NFS服务器上的文件，就像访问本地文件一样==
- nfs适用于Linux与Unix之间实现文件共享，不能实现Linux与Windows间的文件共享功能
- ==nfs是运行在应用层的协议，其监听于2049/tcp和2049/udp套接字上==
- nfs服务只能基于IP进行认证

### **1.2 nfs的应用场景**

nfs有很多实际应用场景，以下是一些常用的场景：

- ==多个机器共享一台CDROM或其他设备。这对于在多台机器中安装软件来说更加便宜与方便==
- 在大型网络中，配置一台中心NFS服务器用来放置所有用户的home目录可能会带来便利。这些目录能被输出到网络以便用户不管在哪台工作站上登录，总能得到相同的home目录
- 不同客户端可在NFS上观看影视文件，节省本地空间
- 在客户端完成的工作数据，可以备份保存到NFS服务器上用户自己的路径下

nfs体系至少有两个主要部分：

- 一台nfs服务器
- 若干台客户机 

![[Pasted image 20250525064016.png]]

客户机通过TCP/IP网络远程访问存放在NFS服务器上的数据  
在NFS服务器正式启用前，需要根据实际环境和需求，配置一些NFS参数
nfs是基于rpc来实现网络文件系统共享的