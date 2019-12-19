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

### 配置host

```
183.134.11.139    k8s-node1
183.134.11.146  k8s-master1
```

### 关闭防火墙

systemctl disable firewalld.service

systemctl stop firewalld.service

### 配置路由转发

```
cat >> /etc/sysctl.conf << EOF 
net.bridge.bridge-nf-call-ip6tables = 1 
net.bridge.bridge-nf-call-iptables = 1 
net.bridge.bridge-nf-call-arptables = 0 
net.ipv4.ip_forward = 1
EOF
```

### 关闭交换空间

```
swapoff -a
```

### 配置centos 源

```
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

### 配置kuernetes源

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo

[kubernetes]

name=Kubernetes

baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/

enabled=1

gpgcheck=1

repo_gpgcheck=1

gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

EOF
```

## 公共服务安装

### 安装docker

* 安装docker 1.13.1

* ```
  yum install -y docker

  systemctl enable docker

  systemctl start docker
  ```

* 配置docker-hub镜像

```
vi /etc/docker/daemon.json

{

"registry-mirrors": ["https://registry.docker-cn.com"]

}

systemctl daemon-reload
systemctl restart docker
```

### 安装k8s组件

```
yum install -y kubeadm kubectl kubelet
systemctl enable kubelet && systemctl start kubelet
```



