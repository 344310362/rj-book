## 引言

kubernetes是一个开源的，用于管理多个主机上的容器化的应用，让部署容器化的应用简单且高效，Kubernetes提供了应用部署，规划，更新，维护的一种机制。它的前身是谷歌的Borg系统，在Google内部，容器技术已经应用了很多年，Borg系统运行管理着成千上万的容器应用。

## Kubernetes架构

kubernetes集群分为master节点和node节点

### Master

负责管理整个集群的资源调度，有如下组件：

#### kube-apiserver



