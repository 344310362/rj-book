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

### 安装k8s公共组件

```
yum install -y kubeadm kubectl kubelet
systemctl enable kubelet && systemctl start kubelet
```

## Master安装

### 初始化

```
kubeadm init \
--apiserver-advertise-address=183.134.11.146 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.17.0 \
--service-cidr=10.243.0.0/16 \
--pod-network-cidr=10.244.0.0/16
```

### 执行结果

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 183.134.11.146:6443 --token iqgs60.6lp4wd6aet28ncmf \
    --discovery-token-ca-cert-hash sha256:b9450b443c2876f3b6547d214b727ab403e2d261c8cda5f6ea7a7fce3ddd45ea
```

### 执行提示命令配置kubectl

```
mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 查看服务器启动装

* 查看资源
* ```
  kubectl get pods,svc -n kube-system
  kubectl get cs
  ```
* 查看节点
* ```
  kubectl get nodes
  ```

## 节点安装

```
kubeadm join 183.134.11.146:6443 --token iqgs60.6lp4wd6aet28ncmf \
    --discovery-token-ca-cert-hash sha256:b9450b443c2876f3b6547d214b727ab403e2d261c8cda5f6ea7a7fce3ddd45ea
```



