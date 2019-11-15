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

> 因为我的hadoop是3.2.0的，hbase看了版本只支持到hadoop2.7，所以就下载了最新的hbase，折腾了一番不行，后面换了
>
> hbase-1.4.11

### 配置

#### 复制hadoop属性

```
cp core-site.xml hdfs-site.xml /usr/local/hbase/conf/
```

#### hbase-env.sh

```
# 不使用hbase默认的ZK服务
export HBASE_MANAGES_ZK=false
export JAVA_HOME=/usr/local/java/jdk1.8.0_231
export HBASE_CLASSPATH=/usr/local/hadoop/etc/hadoop
# ssh 默认的断口是22，要调整就要修改这恶参数
export HBASE_SSH_OPTS="-p 63501"
```

#### hbase-site.xml

```
<configuration>

<property>
        <name>hbase.master.info.port</name>
        <value>16010</value>
</property>
<!-- 指定hbase在HDFS上存储的路径 -->
<property>
        <name>hbase.rootdir</name>
        <value>hdfs://node1:9000/hbase</value>
</property>
<!-- 指定hbase是分布式的 -->
<property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
</property>
<!-- 指定zk的地址，多个用“,”分割 -->
<property>
        <name>hbase.zookeeper.quorum</name>
        <value>node1:2181</value>
</property>
<property>
        <name>hbase.tmp.dir</name>
        <value>/usr/local/hbase/data/tmp</value>
</property>
```

#### regionservers

```
node2
node3
```

#### backup-masters

```
node1
```

### 拷贝集群信息

```
scp -P 63501 -r hbase/ root@node2:/usr/local/
scp -P 63501 -r hbase/ root@node3:/usr/local/
```

### 启动

```
# 启动 
/usr/local/hbase/bin/../conf/

# 重启 master
hbase-daemon.sh start master
# 重启 region
hbase-daemon.sh restart regionserver
```

### WebUI

10.8.229.42:16010

