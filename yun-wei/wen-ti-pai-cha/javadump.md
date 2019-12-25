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

```
jstack 7027 > jstack.log
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



