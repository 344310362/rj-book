## 引言

> hbase的安装需要依赖到zookeeper，进行master的选举。

## 安装zookeeper

版本：3.5.5，注意选择的是带 bin 的，下面那个是源代码，名字不规范没有加个src，第一次下载后面那个坑到了。

![](/assets/bigdata/zookeeper/view1.png)

### 配置

### 命令

```
# 启动
zkServer.sh start

# client 连接
zkClient.sh -server node1:2181

# 查看
ls /

# 删除
deleteall /hbase
```

## 安装Hbase

### 配置

#### hbase-env.sh

```
# 不使用hbase默认的ZK服务
export HBASE_MANAGES_ZK=false
export JAVA_HOME=/usr/local/java/jdk1.8.0_231
export HBASE_CLASSPATH=/usr/local/hadoop/etc/hadoop
# ssh 默认的断口是22，要调整就要修改这恶参数
export HBASE_SSH_OPTS="-p 63501"
```



