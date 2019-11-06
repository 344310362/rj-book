
## 安装
> erlang 跟 rabbitmq 的版本对应有要求

### erlang
- 下载 erlang-19.0.4-1.el7.centos.x86_64.rpm
    
    https://www.rabbitmq.com/releases/erlang/
    
- 安装

```
rpm -ivh erlang-19.0.4-1.el7.centos.x86_64.rpm 
```
- 查看版本

```
[root@portal-test19 local]# erl -version
Erlang (SMP,ASYNC_THREADS,HIPE) (BEAM) emulator version 8.0.3

```
### 安装 socat
> 安装rabbitmq的时候需要依赖到socat  [socat-1.7.3.2-2.el7.x86_64.rpm](https://centos.pkgs.org/7/centos-x86_64/socat-1.7.3.2-2.el7.x86_64.rpm.html)

### rabbitmq
- 下载 3.6.6 的版本
    - https://github.com/rabbitmq/rabbitmq-server/releases/tag/rabbitmq_v3_6_6
- 安装
    ```
    rpm -ivh rabbitmq-server-3.6.6-1.el6.noarch.rpm 
    ```
- 修改配置
    ```
    # 拷贝配置
    cp /usr/share/doc/rabbitmq-server-3.6.6/ /etc/rabbitmq/
    cd /etc/rabbitmq
    mv rabbitmq.config.example rabbitmq.config
    ```
- 启动
    - service rabbitmq-server start
- 安装web管理端
    - rabbitmq-plugins enable rabbitmq_management

- 创建用户
> 新建一个用户名为admin,密码为admin的用户，并授予管理员（administrator）权限

```
[root@localhost sbin]# rabbitmqctl add_user  admin  admin
[root@localhost sbin]# rabbitmqctl set_user_tags admin administrator
```

- 授权

```
    - rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```

- 修改 WEB UI的访问端口

```

```
# 到 /etc/rabbitmq/rabbitmq.conf 搜索“rabbitmq_management”关键字，把对应的port 改掉

%% Change the port on which the HTTP listener listens,
   %% specifying an interface for the web server to bind to.
   %% Also set the listener to use SSL and provide SSL options.
   %%
   {listener, [{port,     60007},
               {ip,       "0.0.0.0"},
               {ssl,      false}]}
   %% One of 'basic', 'detailed' or 'none'. See

```
重启 rabbit-server 后生效


