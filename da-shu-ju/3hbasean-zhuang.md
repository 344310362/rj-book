## 引言

> hbase的安装需要依赖到zookeeper，进行master的选举。hbase有自带了zookeeper，但是我们这选择了自己安装。

## 安装zookeeper

版本：3.5.5，注意选择的是带 bin 的，下面那个是源代码，名字不规范没有加个src，第一次下载后面那个坑到了。

![](/assets/bigdata/zookeeper/view1.png)

### 配置

略

### 启动

zkServe.sh start

### 客户端访问

zkCli.sh -server node1:2181



## 安装Hbase

* 版本：2.2.2

* 解压/设置环境变量

### 配置

#### hbase-site.xml

```

```

#### hbase-env.sh

```

```

#### hadoop相关配置

复制hadoop core-site.xml 和 hdfs-site.xml 到 hbase的conf目录里面

#### regionservers配置

```
node2
node3
```

#### backup-masters配置

```
node2
```

## 体验

### 启动

```
start-hbase.sh
```



