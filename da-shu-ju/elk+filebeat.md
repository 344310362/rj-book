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

设置filebeat输入，并且输出到elasticsearch，并且设置同一条日志多行合并

```
/etc/logstash/conf.d

input {
    beats {
    port => 63003
}
}
filter {
    multiline {
    pattern => "^%{TIMESTAMP_ISO8601}"
    what => "previous"
    negate => true
    }
}

output{
    if "244.173-vmp-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.173-logstash-tomcat-vmp-%{+yyyy.MM.dd}"
        }
    }

    if "244.173-pontus-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.173-logstash-tomcat-pontus-%{+yyyy.MM.dd}"
        }
    }

    if "244.173-console-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.173-logstash-tomcat-console-%{+yyyy.MM.dd}"
        }
    }

    if "244.173-poseidon-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.173-logstash-tomcat-poseidon-%{+yyyy.MM.dd}"
        }
    }

    if "244.173-aipaas-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.173-logstash-tomcat-aipaas-%{+yyyy.MM.dd}"
        }
    }

    if "244.173-api-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.173-logstash-tomcat-api-%{+yyyy.MM.dd}"
        }
    }

    if "244.174-vmp-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.174-logstash-tomcat-vmp-%{+yyyy.MM.dd}"
        }
    }

    if "244.174-pontus-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.174-logstash-tomcat-pontus-%{+yyyy.MM.dd}"
        }
    }

    if "244.174-console-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.174-logstash-tomcat-console-%{+yyyy.MM.dd}"
        }
    }

    if "244.174-poseidon-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.174-logstash-tomcat-poseidon-%{+yyyy.MM.dd}"
        }
    }

    if "244.174-aipaas-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.174-logstash-tomcat-aipaas-%{+yyyy.MM.dd}"
        }
    }

    if "244.174-api-log" in [tags] {
        elasticsearch {
            hosts => ["121.226.244.152:63000"]
            index => "244.174-logstash-tomcat-api-%{+yyyy.MM.dd}"
        }
    }

    stdout {
    codec => rubydebug {}
    }
}
```

multiline 插件安装

插件查看

```
[root@cluster-suqiandatacenter-ahb-0001 conf.d]# /usr/share/logstash/bin/logstash-plugin list
logstash-codec-avro
logstash-codec-cef
logstash-codec-collectd
logstash-codec-dots
logstash-codec-edn
logstash-codec-edn_lines
logstash-codec-es_bulk
logstash-codec-fluent
logstash-codec-graphite
logstash-codec-json
logstash-codec-json_lines
logstash-codec-line
logstash-codec-msgpack
logstash-codec-multiline
logstash-codec-netflow
logstash-codec-plain
logstash-codec-rubydebug
logstash-filter-aggregate
logstash-filter-anonymize
logstash-filter-cidr
logstash-filter-clone
logstash-filter-csv
logstash-filter-date
logstash-filter-de_dot
logstash-filter-dissect
logstash-filter-dns
logstash-filter-drop
logstash-filter-elasticsearch
logstash-filter-fingerprint
logstash-filter-geoip
logstash-filter-grok
logstash-filter-http
logstash-filter-jdbc_static
logstash-filter-jdbc_streaming
```

安装

```
logstash-plugin install logstash-filter-multiline
```



