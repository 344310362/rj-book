## 引言

kubernetes是一个开源的，用于管理多个主机上的容器化的应用，让部署容器化的应用简单且高效，Kubernetes提供了应用部署，规划，更新，维护的一种机制。它的前身是谷歌的Borg系统，在Google内部，容器技术已经应用了很多年，Borg系统运行管理着成千上万的容器应用。

## Kubernetes架构

kubernetes集群分为master节点和node节点

### Master组件

负责管理整个集群的资源调度，有如下组件：

#### kube-apiserver

集群对外提供的api服务，它是 Kubernetes 控制面的前端。

#### etcd

etcd 是兼具一致性和高可用性的键值数据库，可以作为保存 Kubernetes 所有集群数据的后台数据库。

#### kube-scheduler

根据 硬件/软件/策略约束、亲和性和反亲和性规范、数据位置等对资源的调度进行决策。该组件监视那些新创建的未指定运行节点的 Pod，并选择节点让 Pod 在上面运行。

#### kube-controller-manager

监控集群上面的节点资源，自动的进行伸缩控制。

这些控制器包括:

* 节点控制器（Node Controller）: 负责在节点出现故障时进行通知和响应。
* 副本控制器（Replication Controller）: 负责为系统中的每个副本控制器对象维护正确数量的 Pod。
* 端点控制器（Endpoints Controller）: 填充端点\(Endpoints\)对象\(即加入 Service 与 Pod\)。
* 服务帐户和令牌控制器（Service Account &Token Controllers）: 为新的命名空间创建默认帐户和 API 访问令牌。

### Node组件

#### kubelet

master将pod分配给某个具体的node节点，node上面的代理根据PodSpecs的描述，负责将pod具体的部署起来。

kube-proxy



  


  




