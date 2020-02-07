## 引言

> VisualVm 是java官方提供的一个性能监控工具，一般在jdk/bin 目录下面就有对应的exe应用。它是借助JMX代理来监控java应用的。

visualvm 上面有2种连接，jmx和jstatd,网上关于使用visualvm很杂，有的说要改策略，起jstatd，设置密码，改jvm启动参数一系列。其实这些不是都要配置的，你用什么样的监控模式就配置哪些就好。

## JMX连接

JMX\(ava Management Extensions\)，是一个为应用程序植入管理功能的框架。用户可以在任何Java应用程序中使用这些代理和服务实现管理。

### tomcat应用开启jmx

需要在catalina.sh 中添加如下配置：

```
JAVA_OPTS="$JAVA_OPTS -Djava.rmi.server.hostname=192.168.54.5"  ## 配置远程的主机
JAVA_OPTS="$JAVA_OPTS -Dcom.sun.management.jmxremote.port=8888"  ## 配置远程jMX端口
JAVA_OPTS="$JAVA_OPTS -Dcom.sun.management.jmxremote.rmi.port=8888"  ## 配置远程jMX端口
JAVA_OPTS="$JAVA_OPTS -Dcom.sun.management.jmxremote.authenticat=true"  ## ## 配置远程jMX 是否需要认证
JAVA_OPTS="$JAVA_OPTS -Dcom.sun.management.jmxremote.ssl=false" ## 配置远程jMX是否需要ssl
```
jmxremote密码设置（也可以不），$JAVA_HOME/jre/lib/management/jmxremote.password.template,复制一份并改名为jmxremote.password,然后修改只读权限并编辑jmxremote.passwrod，取消以下两行注释：
```
#monitorRole QED
#controlRole R&D
```


### jar应用开启JMX

```
nohup
java
-Djava.rmi.server.hostname=1.1.1.1
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port=60001
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
-Dconfig=websocket.properties
-Dport=9301
-Dlogback.configurationFile=/usr/local/cloudv/component/websocket1/logback.xml
-jar
websocket1.jar
&
```

## Jstatd连接

JVM jstat Daemon：守护进程，一个RMI服务器程序，用于监控本地所有JVM从创建开始直到销毁整个过程中的资源使用情况，同时提供接口给监控工具（如这里的VisualVM），让工具能连接到本机所有的JVM。

### 创建安全策略

由于jstatd server没有提供任何对远程client端的认证，客户端程序获取到本地当前用户的所有JVM信息后可能存在安全隐患，所以jstatd要求启动之前必须指定本地安全策略，否则jstatd进程无法启动。

新建文件 jstatd-all.policy 添加如下：

```
grant codebase "file:/home/intsmaze/jdk1.8.0_144/lib/tools.jar" {
permission java.security.AllPermission;
};
```

### 启动jstatd服务

```
./jstatd -J-Djava.security.policy=/usr/local/jdk1.8.0/bin/jstatd-all.policy -J-Djava.rmi.server.logCalls=true
```

## Jmx与Jstatd连接的区别 {#jmx连接与jstatd连接的区别}

jmx一般是监控某个应用的，jstatd可以监控整台机器的所有jvm应用。jstatd还需要远程服务器开启jstatd服务。

