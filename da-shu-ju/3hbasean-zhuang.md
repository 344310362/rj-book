## 引言

> hbase的安装需要依赖到zookeeper，进行master的选举。

## 安装zookeeper

版本：3.5.5，注意选择的是带 bin 的，下面那个是源代码，名字不规范没有加个src，第一次下载后面那个坑到了。

![](/assets/bigdata/zookeeper/view1.png)

### 配置

zoo.cfg

```
# 目前之启了一台资源有限
server.1=node1:2888:3888
```

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

### HBase shell

与`MySQL`不同,`HBase`是面向列的数据库. 通常会将某些列存储在不同的文件内. 比如`<id,name,age,sex>`有时会被拆分成`<id,name>`

`<id,age,sex>`分布在不同的文件内。

```
[root@node1 home]# hbase shell
HBase Shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
Version 1.4.11, r927981c8a0367b6d0ec2e6c94f12d57592dfa5e3, Fri Oct 18 01:04:38 CDT 2019

hbase(main):001:0> list
TABLE                                                                                                                                                                                                                                                                         
0 row(s) in 0.8180 seconds

=> []
```

#### 建表

```
create 'user', 'info1','info2'
```

#### 查看表信息

```
hbase(main):018:0> describe 'user'
Table user is ENABLED                                                                                                                                                                                                                                                         
user                                                                                                                                                                                                                                                                          
COLUMN FAMILIES DESCRIPTION                                                                                                                                                                                                                                                   
{NAME => 'info1', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0
'}                                                                                                                                                                                                                                                                            
{NAME => 'info2', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0
'}                                                                                                                                                                                                                                                                            
2 row(s) in 0.0430 seconds
```

#### 插入/查看

```
hbase(main):026:0> put 'user','1234','info1:name','zhourj'
0 row(s) in 0.4040 seconds


hbase(main):027:0> scan 'user'
ROW                                                                  COLUMN+CELL                                                                                                                                                                                              
 1234                                                                column=info1:name, timestamp=1573808076442, value=zhourj                                                                                                                                                 
1 row(s) in 0.1600 seconds


hbase(main):028:0> get 'user','1234'
COLUMN                                                               CELL                                                                                                                                                                                                     
 info1:name                                                          timestamp=1573808076442, value=zhourj                                                                                                                                                                    
1 row(s) in 0.1090 seconds


hbase(main):030:0> put 'user','1234','info1:age','23'
0 row(s) in 0.0350 seconds

hbase(main):031:0> get 'user','1234'
COLUMN                                                               CELL                                                                                                                                                                                                     
 info1:age                                                           timestamp=1573808265617, value=23                                                                                                                                                                        
 info1:name                                                          timestamp=1573808076442, value=zhourj                                                                                                                                                                    
1 row(s) in 0.0290 seconds
```



