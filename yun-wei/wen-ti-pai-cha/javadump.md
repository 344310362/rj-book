## 线上堆栈信息dump
> jstack(查看线程)、jmap(查看内存)和jstat(性能分析)

### 内存快照dump

* 查看指定程序的进程id
  ```
    ps -aux|grep ecp-portal-pontus
    -- 也可以用 jps查看，就是jps的结果如果程序没有一个清晰的项目名不好查看
  ```
* 用jmap dump
  ```
    sudo -u tomcat jmap -dump:format=b,file=/usr/local/ecp-portal-pontus/heap.bin 29903
  ```
  
### 线程dump
1. 通过ps或者top找出自己想要的进程id
2. top -Hp pid 找出进程id里面最耗费的CPU的线程ID
3. printf "%x\n" 21742 线程ID转成16进制
4. 打印进程的堆栈信息，查找对应的线程ID信息，然后排查
```
# 打印进程 21711 信息到日志
jstack 21711 > jstack.log
# 直接过滤21711进程里面的线程54ee信息
jstack 21711 | grep 54ee
```

---

> 注意事项

* jmap 要以运行用户的身份执行
  ```
    sudo -u tomcat xxxx
  ```
* 注意 jmap的输出路径 用户有没有权限
  ```
    chown -R tomcat:tomcat ecp-portal-pontus/
  ```
* tomcat 这个用户 查了 /etc/passwd  里面是 /sbin/nologin  所以 su tomcat 是切不过去的，除非更改 /ect/passwd  或者直接用 sudo -u tomcat



