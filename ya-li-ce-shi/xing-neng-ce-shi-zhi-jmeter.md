## 引言

> Jmeter是一款纯java编写的性能测试开源工具软件，它轻巧且功能强大，是主流的压测工具之一。

## 安装

> 以下是笔者操作中用到的相关版本，具体可以灵活选择

* JDK8
* Jmeter 5.3.1

### 准备好java环境

### 安装Jmeter

* 下载地址：[http://jmeter.apache.org/download\_jmeter.cgi](http://jmeter.apache.org/download_jmeter.cgi)
* 选择安装包：apache-jmeter-5.2.1.zip
* 选择一个目录加压即可

### 汉化

* 进路安装目录下面的/bin，找到 jmeter.properties
* 修改 language=zh\_CN

![](/assets/yace/jmeter.png)

## 使用

### 场景说明

笔者这边对一个websocket服务进行性能测试，jmeter本身是不支持websocket的，但是它有一个插件功能，添加指定的插件进来就行了，除了协议上面的不通之外，其他都和http的压测差不多。

### 添加websocket依赖的插件
下载到以下jar放到 /lib/ext 里面

- jmeter官网下载
    - jmeter-websocket-samplers-1.2.2.jar

- maven仓库下载
    - websocket-common-9.4.20.v20190813.jar
    - websocket-api-9.4.20.v20190813.jar
    - websocket-client-9.4.20.v20190813.jar
    - jetty-util-9.4.20.v20190813.jar
    - jetty-io-9.4.20.v20190813.jar
    - jetty-http-9.4.20.v20190813.jar
    - jmeter-plugins-manager-1.3.jar
    - jmeter-websocket-samplers-1.2.2.jar

