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

* jmeter官网下载

  * jmeter-websocket-samplers-1.2.2.jar

* maven仓库下载

  * websocket-common-9.4.20.v20190813.jar
  * websocket-api-9.4.20.v20190813.jar
  * websocket-client-9.4.20.v20190813.jar
  * jetty-util-9.4.20.v20190813.jar
  * jetty-io-9.4.20.v20190813.jar
  * jetty-http-9.4.20.v20190813.jar
  * jmeter-plugins-manager-1.3.jar
  * jmeter-websocket-samplers-1.2.2.jar

### 本次用到的Jmeter组件说明

* 参数

  * 配置元件-用户定义的变量    ：变量写死在jmeter
  * 配置元件-csv数据文件    ：变量定义在文本中，使用多数据场景
  * 后置处理器-json提取器    ：解析上一个的请求结果，作为下面的参数

* 登录

  1. 借助 取样器-BeanShell取样器，编写业务逻辑生成签名
  2. 将签名作为下面请求的入参

```
  import org.apache.commons.codec.digest.DigestUtils;


  log.info("日志输出");

  // 截取10位的时间戳，本来是13位
  String timeStamp = vars.get("timeStamp");
  timeStamp = timeStamp.substring(0,10);
  vars.put("timeStamp",timeStamp);
  // 32位随机数
  String requestId = vars.get("requestId");
  requestId = requestId.replaceAll("-","");
  vars.put("requestId",requestId);
  // 用户ID
  String userId = vars.get("userId");
  // 密钥
  String secretKey = vars.get("secretKey");
  // 授权方式
  String grantType = vars.get("grantType");

  log.info("\n\n ==============生成签名=====================");
  // 生成签名
  String signature = DigestUtils.md5Hex(secretKey+userId+timeStamp+requestId+grantType);
  vars.put("signature",signature);

  log.info("timeStamp:"+timeStamp);
  log.info("requestId:"+requestId);
  log.info("userId:"+userId);
  log.info("secretKey:"+secretKey);
  log.info("grantType:"+grantType);
  log.info("signature:"+signature);
  log.info("\n=============================\n")
```

* 监控
  * 查看结果树：可以看每个请求的详细信息
  * 汇总报告：全局获知请求的成功失败耗时情况

![](/assets/yace/project.png)

