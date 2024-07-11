# 背景

最近，阿煜上线了一个博客服务，刚开始还好，流量不是很大，但之后一不小心成为网红程序员，
搭建博客的2g阿里云服务器一下就顶不住了。这时候最简单的办法就是扩充云服务器，这样的解决方案目前还是能接受的。
然而，之后阿煜又想上线商城服务、音乐服务、小游戏服务等等应用，各个服务的需求还不一样，例如商城服务要求内存10个g，音乐服务只能指定ip访问等等，
这就导致**每次各应用的更新和维护都需要阿煜手动连接各个服务器，手敲命令去挨着调整**，不仅消耗了谈恋爱的时间，还容易出错，瞬间就绷不住了。

**“没有什么问题是加中间层解决不了的，如果有，就再加一层”**

kubernates就是客户端和服务器之间的中间层。
![](https://pic.imgdb.cn/item/663ec6750ea9cb140336831d.png)

# 定义
![](https://pic.imgdb.cn/item/663eca120ea9cb14033b2ebd.png)
这个名字源自希腊语，意为“舵手”或“驾驶员”。在航海中，“Kubernetes”是指船上的舵手，负责指挥船只的航行方向。这个名字恰如其分地表达了 Kubernetes 的设计目标，就像舵手指导船只航行一样，Kubernetes 作为一个容器编排平台，指导着容器化应用程序在分布式系统中的运行和管理。大多数时候我们将中间的8个字符省略，简称为**k8s**。
Kubernetes是一个开源的容器管理平台，它可以自动化部署、扩展和管理容器应用程序。它源自Google，现在由Cloud Native Computing Foundation（CNCF）维护。
Kubernetes的主要优势在于其强大的**集群管理能力**。使用Kubernetes，你可以在集群中轻松部署和扩展应用，而无需担心底层的基础设施。

Kubernetes的主要特性包括：
- **服务发现和负载均衡：** Kubernetes可以自动将网络流量分配给合适的容器。
- **自动装箱：** Kubernetes可以根据资源需求和约束自动决定在哪个主机上启动容器。
- **容器恢复：**在一个集群中，常会因为宿主主机或者OS的问题，导致容器本身不可用，Kubernetes 会自动地对这些不可用的容器进行恢复。
- **自动化滚动和回滚：** Kubernetes可以处理应用的更新和回滚，确保在整个过程中应用始终可用。
- **支持水平伸缩**。

# Kubernates架构
![](https://pic.imgdb.cn/item/668fe982d9c307b7e9a786ea.png)

# 核心组件
## Work Node
### **Node**
一个node就是一个虚拟机或者物理机。
### **Pod**
- 一个node中可以运行一个或者多个pod，**pod是k8s的最小调度单元，也是k8s扩缩容的最小单位**。各个pod共享了node中的环境资源，比如网络、存储和一些运行时配置等。
![](https://pic.imgdb.cn/item/663eced20ea9cb140340c789.png)
- 一个pod可以放一个或者多个容器，通常被设计成只放在一个容器。
  - 要注意的是，一个pod中包含一个“根容器”，或者叫做**Pause容器**，除此之外，还包括一个或者多个业务容器。
  - 同时，一个pod通常只运行在一个node上，不能跨node运行。
  - 另外，一个pod会被分配集群中唯一的ip地址，各pod都会拥有自己的ip、主机名和进程。

> **Pause容器有什么用呢？**
主要是为了给业务容器提供namespace，让所有业务容器共享资源，例如pid，net，init进程等。
###  **container runtime**、**kubelet**、**kube-proxy**
- container runtime：容器运行需要的环境，可理解为一堆软件，拉取镜像、启动镜像等等。
  - docker-Engine
  - Containerd
  - 其他container runtime
- kubelet：和apiserver交互，管理容器
- kube-proxy：为pod提供网络代理和负载均衡

## Master Node
### api-server
- 系统入口，所有请求都会经过它，然后和各个组件进行交互。
例如使用kubectl输入命令查询集群状态就是和api-server交互。
- 所有资源对象操作的权限控制。
### scheduler
- 调度pod。例如将新建的pos调度到相对空闲的Node上。
### controller manager
- **管理集群中各种资源对象的状态。**
若pod故障，重新启动pod或者使用其他pod替代它，就是cm要做的事。
### etcd
- **高可用键值对存储系统。**
存储集群中各种资源对象的状态，是整个集群的数据中心，cm通过它来管理pod。
### cloud controller manager
- 负责与云平台api交互。



# 参考资料
- [从零开始入门 K8s：详解 K8s 核心概念](https://www.infoq.cn/article/knmavdo3jxs3qpkqtzbw)
- [Kubernetes(k8s)是什么？架构是怎么样的？6分钟快速入门](https://www.bilibili.com/video/BV1Du4m137pK)
- [Kubernetes一小时轻松入门](https://www.bilibili.com/video/BV1Se411r7vY)
- https://kubernetes.io/docs/concepts/