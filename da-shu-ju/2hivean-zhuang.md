## 引言

```
安装Hive前，我们应该先考虑下Hadoop与Hive的版本兼容问题。大数据生态的组件很多，后续新增其它组件也要优先考虑此类问题。
```

### hadoop与hbase哪些版本兼容

官方的下载页面有对应说明，我们上一篇的Hadoop是3.2.1，所以Hive我们选择 3.1.2

* 版本说明：[http://hive.apache.org/downloads.html](http://hive.apache.org/downloads.html)

* 下载地址：[https://mirrors.tuna.tsinghua.edu.cn/apache/hive/](https://mirrors.tuna.tsinghua.edu.cn/apache/hive/)

## 安装MySQL

```
Mysql在这边是用来存储Hive的元数据，即Hive的表结构信息，如果之前有别的数据库了可以不用重新安装。
```

* mysql新建schema hive
* 保证机器有链接数据库的权限

## Hive安装

* 新建目录

```
mkdir /usr/local/hive
```
* 解压软件

```
tar -zxvf apache-hive-3.1.2-bin.tar.gz -C /usr/local/hive

```
* 配置环境变量

```
vi /etc/profile
## 在之前hadoop的基础上面添加了hive的信息

HIVE_HOME=/usr/local/hive/apache-hive-3.1.2-bin
JAVA_HOME=/usr/local/java/jdk1.8.0_231
HADOOP_HOME=/usr/local/hadoop
CLASS_PATH=.:$JAVA_HOME/lib
PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH:$HIVE_HOME/bin
export JAVA_HOME ZOOKEEPER_HOME HADOOP_HOME CLASS_PATH PATH
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native

source /etc/profile
```



