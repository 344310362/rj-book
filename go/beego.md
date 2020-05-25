# 安装

Golang 环境默认安装

**安装beego源码：**go get github.com/astaxie/beego
**升级beego源码：**go get -u github.com/astaxie/beego
**安装bee工具：**go get github.com/beego/bee

_安装完毕后，需要将$GOPATH/bin添加到环境变量中，便于在所有路径下使用bee命令_

如果 beego 下载很慢，hosts 修改如下：

具体的 ip 根据 [IP查找工具](https://www.ipaddress.com/ip-lookup) 获取
```
192.30.253.112 github.com
151.101.185.194 github.global.ssl.fastly.net
```

# beego 项目结构说明


```
myproject  //项目文件夹
├── conf  //配置文件夹
│   └── app.conf //beego项目配置文件
├── controllers  //控制器文件夹
│   └── default.go //默认的控制器
├── main.go  //主程序入口
├── models  //数据库相关文件夹
├── routers //路由文件夹
│   └── router.go  //默认路由文件
├── static  //静态文件夹
│   ├── css
│   ├── img
│   └── js
├── tests  //单元测试文件夹
│   └── default_test.go  //默认的单元测试文件
└── views //模版文件夹
    └── index.tpl  //默认的模版文件
```




