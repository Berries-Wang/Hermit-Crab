# [K8S组件介绍](https://kubernetes.io/zh/docs/concepts/overview/components/)
## 下图表展示了包含所有相互关联组件的 Kubernetes 集群。
<img src="./pics/components-of-kubernetes.svg">

&nbsp;&nbsp;一个Kubernetes集群是由一组被称作为节点的机器组成。这些节点上运行Kubernetes所管理的容器化应用。集群至少一个工作节点。

&nbsp;&nbsp;工作节点托管作为应用负载的组件Pod，控制平面管理集群中的工作节点和Pod。为集群提供故障转移和高可用，这些控制平面一般跨多主机运行，集群跨多个节点运行。

## 组件介绍
### 控制平面组件（Control Plane Components）
&nbsp;&nbsp;控制平面的组件对集群做出全局决策(如调度)，以及检测和响应集群事件（如，当不满足部署的replicas字段时，启动新的pod）

&nbsp;&nbsp;控制平面可以在集群中的任何节点上运行。 然而，为了简单起见，设置脚本通常会在同一个计算机上启动所有控制平面组件， 并且不会在此计算机上运行用户容器。

#### kube-apiserver
&nbsp;&nbsp;API服务器是Kubernetes控制平面的组件，该组件公布了Kubernetes API。API服务器是Kubernetes控制平面的前端。

&nbsp;&nbsp;Kubernetes API服务器的主要实现是kube-apiserver.kube-apiserver设计上考虑了水平伸缩，也就是，他可以通过部署多个实例进行伸缩。**可以运行kube-apiserver的多个实例，并在这些实例之间平衡流量**

#### etcd
&nbsp;&nbsp;etcd是一致性和高可用性的键值数据库，可以作为保存Kubernetes所有集群数据的后台数据库。

#### kube-scheduler
&nbsp;&nbsp;控制平面组件，负责监视新创建的、未指定运行节点（node）的pod，选择节点让Pod在上面运行。（调度决策考虑的因素包括单个 Pod 和 Pod 集合的资源需求、硬件/软件/策略约束、亲和性和反亲和性规范、数据位置、工作负载间的干扰和最后时限）

#### kube-controller-manager
&nbsp;&nbsp;在主节点上运行控制器的组件。

&nbsp;&nbsp;在逻辑上说，每个控制器都是一个单独的进程，但是为了降低复杂性，他们都被编译到了同一个可执行文件中，并在一个进程中运行。这些控制器包括：
+ 节点控制器（Node Controller）: 负责在节点出现故障时进行通知和响应
+ 任务控制器（Job controller）: 监测代表一次性任务的 Job 对象，然后创建 Pods 来运行这些任务直至完成
+ 端点控制器（Endpoints Controller）: 填充端点(Endpoints)对象(即加入 Service 与 Pod)
+ 服务帐户和令牌控制器（Service Account & Token Controllers）: 为新的命名空间创建默认帐户和 API 访问令牌

#### cloud-controller-manager 
&nbsp;&nbsp;云控制器管理器是指嵌入特定云的控制逻辑的 控制平面组件。 云控制器管理器允许您链接集群到云提供商的应用编程接口中， 并把和该云平台交互的组件与只和您的集群交互的组件分离开。

&nbsp;&nbsp;cloud-controller-manager 仅运行特定于云平台的控制回路。 如果你在自己的环境中运行 Kubernetes，或者在本地计算机中运行学习环境， 所部署的环境中不需要云控制器管理器。

&nbsp;&nbsp;与 kube-controller-manager 类似，cloud-controller-manager 将若干逻辑上独立的 控制回路组合到同一个可执行文件中，供你以同一进程的方式运行。 你可以对其执行水平扩容（运行不止一个副本）以提升性能或者增强容错能力。

&nbsp;&nbsp;下面的控制器都包含对云平台驱动的依赖：
+ 节点控制器（Node Controller）: 用于在节点终止响应后检查云提供商以确定节点是否已被删除
+ 路由控制器（Route Controller）: 用于在底层云基础架构中设置路由
+ 服务控制器（Service Controller）: 用于创建、更新和删除云提供商负载均衡器


### Node组件
&nbsp;&nbsp;节点组件在每个节点上运行，维护运行的Pod并提供Kubernetes 运行环境

#### kubelet
&nbsp;&nbsp;一个在集群中每个节点（Node）上运行的代理，他保证容器都运行在pod中。

&nbsp;&nbsp;**kubelet接受一组通过各类机制提供给他的PodSpecs**，确保这些PodSpecs中描述的容器处理运行状态且健康。kubelet不会管理不是由Kubernetes 创建的容器

#### kube-proxy
&nbsp;&nbsp; kube-proxy 是集群中每个节点上运行的**网络代理**， 实现 Kubernetes 服务（Service） 概念的一部分。

&nbsp;&nbsp;kube-proxy 维护节点上的网络规则。这些网络规则允许从集群内部或外部的网络会话与 Pod 进行网络通信。如果操作系统提供了数据包过滤层并可用的话，kube-proxy 会通过它来实现网络规则。否则， kube-proxy 仅转发流量本身。

#### 容器运行时（Container Runtime）
&nbsp;&nbsp;容器运行环境是负责运行容器的软件。

&nbsp;&nbsp;Kubernetes 支持多个容器运行环境: Docker、 containerd、CRI-O 以及任何实现 Kubernetes CRI (容器运行环境接口)。


### 插件（Addons）
&nbsp;&nbsp;插件使用Kubernets资源（DaemonSet，Deployment等）实现集群功能。因为这些插件提供集群级别的功能。


----------------------------
## 参考资料
1. 最好的文档： 官方文档[https://kubernetes.io/zh/docs/concepts/overview/components/#control-plane-components](https://kubernetes.io/zh/docs/concepts/overview/components/#control-plane-components)