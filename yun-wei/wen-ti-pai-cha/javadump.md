## 线上堆栈信息dump

- 查看指定程序的进程id
    ~~~
    ps -aux|grep ecp-portal-pontus
    ~~~
- 用jmap dump
    ~~~
    sudo -u tomcat jmap -dump:format=b,file=/usr/local/ecp-portal-pontus/heap.bin 29903
    ~~~
---
>注意事项

- jmap 要以运行用户的身份执行
    ~~~
    sudo -u tomcat xxxx
    ~~~
- 注意 jmap的输出路径 用户有没有权限
    ~~~
    chown -R tomcat:tomcat ecp-portal-pontus/
    ~~~
- tomcat 这个用户 查了 /etc/passwd  里面是 /sbin/nologin  所以 su tomcat 是切不过去的，除非更改 /ect/passwd  或者直接用 sudo -u tomcat
