## 引言

> 随着架构微服务的趋势，应用数量的不断扩大，应用部署的机器也可能不尽相同，给日志查看带来了不少麻烦，业内比较流行的解决方案就是ELK

## ELASTIC

* elasticsearch  负责日志的搜索和存储
* filebeat            负责日志的采集 golang编写的轻量级应用
* logstash          负责日志的增强处理（格式转换/无用信息过滤等）
* kibana              负责日志的展示，也提供了不错的图表功能

## 使用

多台服务器，每台服务器上面有多个不同应用，各个服务器上面都安装filebeat，统一采集日志发往logstash。

![](/assets/bigdata/elk/filebeat.png)

### 安装

忽略

### 配置

#### filebeat

在各个应用所在的机子上指定采集的输入

```
/etc/filebeat

#=========================== Filebeat inputs =============================

filebeat.inputs:

# Each - is an input. Most options can be set at the input level, so
# you can use different inputs for various configurations.
# Below are the input specific configurations.

- type: log

  # Change to true to enable this input configuration.
  enabled: true

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /usr/local/ecp-portal-pontus/logs/catalina.out
  tags: ["244.173-pontus-log"]

- type: log
  enabled: true
  paths:
    - /usr/local/ecp-portal-console/logs/catalina.out
  tags: ["244.173-console-log"]

- type: log
  enabled: true
  paths:
    - /usr/local/ecp-portal-vmp/logs/catalina.out
  tags: ["244.173-vmp-log"]

- type: log
  enabled: true
  paths:
    - /usr/local/ecp-portal-poseidon/logs/catalina.out
  tags: ["244.173-poseidon-log"]

- type: log
  enabled: true
  paths:
    - /usr/local/ecp-portal-aipaas/logs/catalina.out
  tags: ["244.173-aipaas-log"]

- type: log
  enabled: true
  paths:
    - /usr/local/ecp-portal-api/logs/catalina.out
  tags: ["244.173-api-log"]
```

指定采集输出到logstash

```
#----------------------------- Logstash output --------------------------------
output.logstash:
  # The Logstash hosts
  hosts: ["121.226.244.152:63003"]

  # Optional SSL. By default is off.
  # List of root certificates for HTTPS server verifications
  #ssl.certificate_authorities: ["/etc/pki/root/ca.pem"]

  # Certificate for SSL client authentication
  #ssl.certificate: "/etc/pki/client/cert.pem"

  # Client Certificate Key
  #ssl.key: "/etc/pki/client/cert.key"

#================================ Processors =====================================
```

#### logstash



