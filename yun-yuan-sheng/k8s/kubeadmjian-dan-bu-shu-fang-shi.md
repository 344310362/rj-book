## 引言

> kubeadm简单安装k8s集群

## 集群规划

| ip | hostname |
| :--- | :--- |
| 183.134.11.146 | k8s-master |
| 183.134.11.139 | k8s-node |

## 系统初始化

### 配置主机名

```
hostnamectl --static set-hostname k8s-master1
hostnamectl --static set-hostname k8s-node1
```



