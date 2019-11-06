## 引言

> hadoop是java语言编写的，所以部署的时候要依赖到java环境，hadoop不同版本对jdk是有不同要求的，hadoop 3.x 的就要求要用到jdk8的版本。hadoop集群的namenode是通过SSH来启动和停止各节点上面的各种守护进程，所以节点之间需要免密登录。

### 参考

* 官网说明：[https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)
* hadoop下载地址：[https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/current2/](https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/current2/)

## 环境准备

节点机器

* 机器hostname分别设置成node1/node2/node3,并且/etc/hosts添加如下路由信息

```
10.8.229.42    node1
10.8.229.43    node2
10.8.229.44     node3
```

软件版本

* java: jdk1.8.0\_231
* hadoop: 3.2.1



