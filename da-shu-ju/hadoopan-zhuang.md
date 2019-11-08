## 引言

> hadoop是java语言编写的，所以部署的时候要依赖到java环境，hadoop不同版本对jdk是有不同要求的，hadoop 3.x 的就要求要用到jdk8的版本。hadoop集群的namenode是通过SSH来启动和停止各节点上面的各种守护进程，所以节点之间需要免密登录。

### 参考

* 官网说明：[https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)
* hadoop下载地址：[https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/current2/](https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/current2/)

## 环境准备

### 节点机器

* 机器hostname分别设置成node1/node2/node3,并且/etc/hosts添加如下路由信息

```
10.8.229.42    node1
10.8.229.43    node2
10.8.229.44    node3
```

### 软件版本

* java: jdk1.8.0\_231【如果机器是默认的openjdk7，就先卸载后自己安装，这里不做详细说明】
* hadoop: 3.2.1

### SSH

检查是否安装了SSH

```
rpm -qa|grep ssh
```

设置免密登录

1.创建ssh-key，这里我们采用rsa方式，使用如下命令：【一路回车】

```
ssh-keygen -t rsa -P ""
```

2.将自己也设置成免密登录

```
cat ~/.ssh/id_rsa.pub >> authorized_keys
```

3.将公钥发送到需要免密登录的机器上

```
ssh-copy-id -p 63501 node2
ssh-copy-id -p 63501 node3
```

4.无密码验证登录

```
ssh -p 63501 localhost // ssh 的端口如果是22就无需要额外指定
```

**注意点**：密钥是根据当前hostname设置的，如果hostname有变化了，之前公钥就用不了了，需要重新设置，重新分发到其它机器。

### 操作用户

笔者的操作使用的是root用户，如果是生产用最好新建一个hadoop用户，并设置相关的权限。_下面是参考的命令，该安装并没有执行下面的命令_。

```
1、首先新建用户，adduser命令

　　sudo adduser hadoop

　　passwd hadoop

　　输入密码之后，一路 y 确定。

2、添加用户组

　　在创建hadoop用户的同时也创建了hadoop用户组，下面我们把hadoop用户加入到hadoop用户组下面

　　sudo usermod -a -G hadoop hadoop

　　前面一个hadoop是组名，后面一个hadoop是用户名。完成后查询

　　cat /etc/group

3、赋予root权限【实验环境可以设置大点】

　　先切换到root的用户

　　sudo nano /etc/sudoers

　　修改文件如下：

　　　　在　　root　　ALL=(ALL)　　ALL　　下面添加：

　　　　　　　hadoop　　ALL=(ALL)　　ALL

　　保存退出，hadoop用户就拥有了root权限了
```

## Hadoop 安装

* 解压软件

```
tar -zxvf hadoop-3.2.1.tar.gz -C /usr/local/
```

* 重命名

```
mv hadoop-3.2.1.tar.gz hadoop
```

* 配置环境变量

```
vi /etc/profile

JAVA_HOME=/usr/local/java/jdk1.8.0_231
HADOOP_HOME=/usr/local/hadoop
CLASS_PATH=.:$JAVA_HOME/lib
PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
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

## Hadoop配置

### 核心配置

* core-site.xml
* hadoop-env.sh
* hdfs-site.xml
* mapred-site.xml
* yarn-site.xml
* workers

### 配置修改

#### 当前工作目录

```
[root@node1 hadoop]# pwd
/usr/local/hadoop/etc/hadoop
```

#### core-site.xml

```
<configuration>
<!-- 指定HDFS老大（namenode）的通信地址 -->
        <property>
            <name>fs.defaultFS</name>
            <value>hdfs://node1:9000</value>
        </property>
        <properyt>
                <name>hadoop.tmp.dir</name>
                <value>/usr/local/hadoop/tmp</value>
        </properyt>
</configuration>
```

#### hadoop-env.sh

```
export JAVA_HOME=/usr/local/java/jdk1.8.0_231
export HADOOP_PID_DIR=/usr/local/hadoop/tmp/pids
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
```

#### hdfs-site.xml

```
<configuration>
    <property>
            <name>dfs.namenode.http-address</name>
            <value>node1:50070</value>
    </property>
    <property>
            <name>dfs.namenode.secondary.http-address</name>
            <value>node2:50090</value>
    </property>


    <!-- <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/local/hadoop/namenode</value>
        <description>namenode上存储hdfs名字空间元数据 </description>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/local/hadoop/datanode</value>
        <description>datanode上数据块的物理存储位置</description>
    </property>-->
    <!-- 设置hdfs副本数量 -->
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>

</configuration>
```

#### mapred-site.xml

```
<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
<property>
  <name>yarn.app.mapreduce.am.env</name>
  <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
<property>
  <name>mapreduce.map.env</name>
  <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
<property>
  <name>mapreduce.reduce.env</name>
  <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>


</configuration>
```

#### yarn-site.xml

```
<configuration>

<!-- Site specific YARN configuration properties -->
<property>
  <name>yarn.resourcemanager.hostname</name>
  <value>node1</value>
</property>
<property>
 <name>yarn.nodemanager.aux-services</name>
 <value>mapreduce_shuffle</value>
</property>

<property>
    <name>yarn.resourcemanager.webapp.address</name>
    <value>node1:8088</value>
</property>
</configuration>
```

#### workers

```
node1
node2
node3
```

## 其它节点

通过 scp 命令把 hadoop 和 java 目录传送到别的节点

```
scp -r /usr/local/hadoop root@node2:/usr/local/hadoop 
scp -r /usr/local/hadoop root@node3:/usr/local/hadoop 

scp -r /usr/local/java root@node2:/usr/local/java 
scp -r /usr/local/java root@node3:/usr/local/java
```

其它环节变量也要在对应的节点设置

* JAVA\_HOME
* HADOOP\_HOME

## Hadoop 启动

### 初始化

```
hdfs namenode -format
```

### 启动和停止脚本

```
脚本所在目录：/usr/local/hadoop/sbin  sbin我们上面已经添加到PATH里面了，所以任何一个地方都能执行
```

* 修改 start-dfs.sh 用户权限

* ```
  HDFS_DATANODE_USER=root
  HDFS_NAMENODE_USER=root
  HDFS_SECONDARYNAMENODE_USER=root
  HDFS_DATANODE_SECURE_USER=root
  ```
* 修改 stop-dfs.sh 用户权限

* ```
  HDFS_DATANODE_USER=root
  HDFS_NAMENODE_USER=root
  HDFS_SECONDARYNAMENODE_USER=root
  HDFS_DATANODE_SECURE_USER=root
  ```
* 自动所有进程

```
start-all.sh
```

* 查看启动的进程

```
[root@node1 sbin]# jps
29536 NodeManager
27348 DataNode
6022 SecondaryNameNode
26873 NameNode
29324 ResourceManager
7023 Jps
```

* 日志查看
  如果相应的进程没有看到，可以查看下日志，具体看日志里面提示了什么

![](/assets/bigdata/hadoop_install_log.png)

## Hadoop访问

### HDFS页面

> 查看NameNode状态; 该端口的定义位于core-default.xml中，可以在hdfs-site.xml 中修改;

![](/assets/bigdata/hadoop_install_view1.png)

### YARN管理界面

> yarn-site.xml 里面可以修改

## ![](/assets/bigdata/hadoop_install_view2.png)体验

hadoop里面自带了mapreduce例子，下面我们执行一个wordcount来计算文本中的单词个数

1. 选取hadoop安装目录的 LICENSE.txt 文件进行统计
2. 将 LICENSE.txt 存在到hadoop的目录中
3. 指定执行算法后查看output目录

```
hadoop fs -mkdir /input
hadoop fs -put LICENSE.txt /input
```

```
[root@node1 mapreduce]# pwd
/usr/local/hadoop/share/hadoop/mapreduce
hadoop jar hadoop-mapreduce-examples-3.2.1.jar wordcount /input /output

2019-11-08 14:01:30,463 INFO client.RMProxy: Connecting to ResourceManager at node1/10.8.229.42:8032
2019-11-08 14:01:32,067 INFO mapreduce.JobResourceUploader: Disabling Erasure Coding for path: /tmp/hadoop-yarn/staging/root/.staging/job_1572962076850_0005
2019-11-08 14:01:32,343 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2019-11-08 14:01:32,717 INFO input.FileInputFormat: Total input files to process : 1
2019-11-08 14:01:32,793 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2019-11-08 14:01:32,973 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2019-11-08 14:01:32,994 INFO mapreduce.JobSubmitter: number of splits:1
2019-11-08 14:01:33,336 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2019-11-08 14:01:33,793 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1572962076850_0005
2019-11-08 14:01:33,794 INFO mapreduce.JobSubmitter: Executing with tokens: []
2019-11-08 14:01:34,364 INFO conf.Configuration: resource-types.xml not found
2019-11-08 14:01:34,365 INFO resource.ResourceUtils: Unable to find 'resource-types.xml'.
2019-11-08 14:01:34,575 INFO impl.YarnClientImpl: Submitted application application_1572962076850_0005
2019-11-08 14:01:34,694 INFO mapreduce.Job: The url to track the job: http://node1:8088/proxy/application_1572962076850_0005/
2019-11-08 14:01:34,696 INFO mapreduce.Job: Running job: job_1572962076850_0005
2019-11-08 14:01:50,101 INFO mapreduce.Job: Job job_1572962076850_0005 running in uber mode : false
2019-11-08 14:01:50,105 INFO mapreduce.Job:  map 0% reduce 0%
2019-11-08 14:02:00,324 INFO mapreduce.Job:  map 100% reduce 0%
2019-11-08 14:02:10,559 INFO mapreduce.Job:  map 100% reduce 100%
2019-11-08 14:02:11,595 INFO mapreduce.Job: Job job_1572962076850_0005 completed successfully
```

```
[root@node1 hadoop]# hadoop fs -ls /output/
Found 3 items
-rw-r--r--   1 root supergroup          0 2019-11-05 22:41 /output/_SUCCESS
-rw-r--r--   1 root supergroup      35324 2019-11-05 22:41 /output/part-r-00000
drwxr-xr-x   - root supergroup          0 2019-11-08 14:02 /output/test2
[root@node1 hadoop]# hadoop fs -ls /output/test2
Found 2 items
-rw-r--r--   1 root supergroup          0 2019-11-08 14:02 /output/test2/_SUCCESS
-rw-r--r--   1 root supergroup      35324 2019-11-08 14:02 /output/test2/part-r-00000
[root@node1 hadoop]# hadoop fs -cat /output/test2/part-r-00000


hardware    1
hardware)    1
harmless    1
has    22
have    24
having    4
held    3
here    2
here.    4
hereafter    4
hereby    30
herein    5
herein,    1
herein.    3
herein.     1
hereof.    3
hereto,    1
hereunder    6
hereunder,    2
hereunder.    4
history    1
history,    1
hold    1
honor    2
how    5
however    2
however,    4
http://angularjs.org    1
http://code.google.com/p/lz4/    1
http://golang.org/LICENSE    1
http://sourceforge.net/projects/slicing-by-8/    1
...
```

## 安全问题

开启防火墙

```
systemctl start firewalld.service
```

添加防火墙规则（对指定ip开放指定端口）

```
# 先开放本机的远程访问端口
xxx

# 9000端口开放
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.229.43" port protocol="tcp" port="9000" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.229.44" port protocol="tcp" port="9000" accept"


# 设置yarn 管理界面的权限
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.229.43" port protocol="tcp" port="8088" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.229.44" port protocol="tcp" port="8088" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.133.45" port protocol="tcp" port="8088" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.149.194" port protocol="tcp" port="8088" accept"

# hdfs 访问界面 
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.133.45" port protocol="tcp" port="50070" accept"

# datanode http address
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.8.133.45" port protocol="tcp" port="9864" accept"

```

重启防火墙，使配置生效

```
systemctl restart firewalld.service
```

查看配置结果，验证配置

```
firewall-cmd --list-all
```

删除规则

```
firewall-cmd --permanent --remove-rich-rule="rule family="ipv4" source address="10.8.229.42" port protocol="tcp" port="8088" accept"

systemctl restart firewalld.service
```



