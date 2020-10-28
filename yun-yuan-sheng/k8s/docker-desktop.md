# 引言







## 下载国内 k8s 相关镜像


```
git clone https://github.com/AliyunContainerService/k8s-for-docker-desktop
```

在 Mac 上执行如下脚本


```
./load_images.sh
```

## 安装 Docker-desktop
1. 官网下载安装
2. 开启 kubernetes
3. 为 docker daemon 配置中国的官方镜像

```
https://register.docker-cn.com
```
4. 根据情况配置 cpu 和 内存



## kubernetes


## 安装 dashboard
上面阿里脚本 `kubernetes-dashboard.yaml` dashboard 安装的命名空间是 `kubernetes-dashboard`

```
kubectl create -f kubernetes-dashboard.yaml

```

### 开启API Server访问代理


```
kubectl proxy
```

### 获取登录Token

1. 获取对应命名空间下的密钥名字
```
kubectl get sa,secrets -n kube-dashboard
```

2. 获取 token

下面的 `kubernetes-dashboard-token-58bcf` 是上一步获取到的名字，然后 describe 查详情

```
kubectl describe secret kubernetes-dashboard-token-58bcf -n kubernetes-dashboard
```


### 访问地址

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/overview?namespace=default

