## 引言

> 安装Hive前，我们应该先考虑下Hadoop与Hive的版本兼容问题。大数据生态的组件很多，后续新增其它组件也要优先考虑此类问题。

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

### 新建目录

```
mkdir /usr/local/hive
```

### 解压软件

```
tar -zxvf apache-hive-3.1.2-bin.tar.gz -C /usr/local/hive
```

### 配置环境变量

```
在之前hadoop的基础上面添加了hive的信息
vi /etc/profile
```

```
## 
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

### 修改Hive配置

```
[root@node1 conf]# pwd
/usr/local/hive/apache-hive-3.1.2-bin/conf
[root@node1 conf]# cp hive-env.sh.template hive-env.sh
[root@node1 conf]# cp hive-default.xml.template hive-site.xml
```

* `hive-env.sh`添加以下内容

```
export JAVA_HOME=/usr/local/java/jdk1.8.0_231
export HADOOP_HOME=/usr/local/hadoop
export HIVE_HOME=/usr/local/hive/apache-hive-3.1.2-bin
```

* 修改`hive-site.xml`

```
## 在比较开始的地方添加这些

<property>
   <name>system:java.io.tmpdir</name>
   <value>/home/root/hdp/tmpdir</value>
</property>

<property>
     <name>system:user.name</name>
     <value>hive</value>
</property>
```

```
<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
</property>
<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>root</value>
    <description>username to use against metastore database</description>
 </property>
<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>UIojkl@908</value>
    <description>password to use against metastore database</description>
</property>
<property>
    <name>hive.metastore.warehouse.dir</name>
    <value>/usr/local/hive/apache-hive-3.1.2-bin/hive_datawarehouse</value>
    <description>location of default database for the warehouse</description>
</property>
<property>
    <name>hive.exec.scratchdir</name>
    <value>/usr/local/hive/apache-hive-3.1.2-bin/hive_data/tmp</value>
    <description>HDFS root scratch dir for Hive jobs which gets created with write all (733) permission. For each connecting user, an HDFS scratch dir: ${hive.exec.scratchdir}/&lt;username&gt; is created, with ${hive.scratch.dir.permission}.</description>
</property>
<property>
    <name>hive.querylog.location</name>
    <value>/usr/local/hive/apache-hive-3.1.2-bin/hive_data/log</value>
    <description>Location of Hive run time structured log file</description>
</property>
```

### 执行Hive

执行hive的命令时候报了下面的错误，显然是guava包的版本冲突。【hadoop和hive的版本是根据官方提示的安装的，竟然还有这种问题】

```
[hadoop@a74f90aecdad tmp]$ hive
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/hive/lib/log4j-slf4j-impl-2.10.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/hadoop/share/hadoop/common/lib/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)V
    at org.apache.hadoop.conf.Configuration.set(Configuration.java:1357)
    at org.apache.hadoop.conf.Configuration.set(Configuration.java:1338)
    at org.apache.hadoop.mapred.JobConf.setJar(JobConf.java:536)
    at org.apache.hadoop.mapred.JobConf.setJarByClass(JobConf.java:554)
    at org.apache.hadoop.mapred.JobConf.<init>(JobConf.java:448)
    at org.apache.hadoop.hive.conf.HiveConf.initialize(HiveConf.java:5141)
    at org.apache.hadoop.hive.conf.HiveConf.<init>(HiveConf.java:5099)
    at org.apache.hadoop.hive.common.LogUtils.initHiveLog4jCommon(LogUtils.java:97)
    at org.apache.hadoop.hive.common.LogUtils.initHiveLog4j(LogUtils.java:81)
    at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:699)
    at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:683)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:498)
    at org.apache.hadoop.util.RunJar.run(RunJar.java:323)
    at org.apache.hadoop.util.RunJar.main(RunJar.java:236)
```

全局搜索了下，发下以下hadoop和hive的guava包版本不一致。

```
[root@node1 ~]# find / -name *guava*
/usr/local/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar
/usr/local/hadoop/share/hadoop/common/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar
/usr/local/hadoop/share/hadoop/hdfs/lib/guava-27.0-jre.jar
/usr/local/hadoop/share/hadoop/hdfs/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar
/usr/local/hive/apache-hive-3.1.2-bin/lib/guava-19.0.jar
/usr/local/hive/apache-hive-3.1.2-bin/lib/jersey-guava-2.25.1.jar
```

hive替换成和hdoop一样的版本

```
[root@node1 lib]# mv /usr/local/hive/apache-hive-3.1.2-bin/lib/guava-19.0.jar /home/back/lib/
[root@node1 lib]# cp /usr/local/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar /usr/local/hive/apache-hive-3.1.2-bin/lib/guava-19.0.jar
```

### 在mysql中hive的schema

在此之前需要创建mysql下的hive数据库

```
schematool -dbType mysql -initSchema
```

## Hive启动

```
hive --service metastore 
```



