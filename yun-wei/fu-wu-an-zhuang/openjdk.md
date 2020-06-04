centos7 yum安装的java-1.8.0-openjdk 没有 javac ，需要安装 devel 版本。只能先卸载yum安装的jdk,重新安装devel 版本的jdk了。

# 卸载

* 查看目前系统中的jdk

**`rpm -qa | grep jdk`**

* 卸载

**`yum -y remove `**`xxx`



# 安装

* 查看安装包

`yum search java|grep jdk`

* 找到64位 1.8 devel版本的

  **java-1.8.0-openjdk-devel.x86\_64**

* 安装

`yum install -y java-1.8.0-openjdk-devel.x86_64`

* JAVA\_HOME 目录

```
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64
```



