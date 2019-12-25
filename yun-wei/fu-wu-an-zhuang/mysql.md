## 安装

### 线上安装

[https://www.cnblogs.com/julyme/p/5969626.html](https://www.cnblogs.com/julyme/p/5969626.html)

* 数据库更改密码
    use mysql;
    mysql &gt; update user set password=password\('\*\*\*\*\*\*\*'\) where user='root';
    mysql &gt; exit;
    mysql&gt; select host,user from user; \(查看用户的权限情况\)
    mysql&gt; select host, user, password from user;
    set password for 'root'@'localhost' =password\('\*\*\*\*'\);    
    Grant all privileges on _._ to 'root'@'%' identified by '\*\*\*\*' with grant option;
    flush privileges;    \(运行此句才生效，或者重启MySQL\)

### 离线安装

* 到官网：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)  下载编译好的二进制包

  * eg:mysql-5.6.37-linux-glibc2.12-x86\_64.tar.gz

    * 安装数据库

    ```
    解压
    　　tar -zxvf mysql-5.6.33-linux-glibc2.5-x86_64.tar.gz
    　　复制解压后的mysql目录
    　　cp -r mysql-5.6.33-linux-glibc2.5-x86_64 /usr/local/mysql
    进入安装mysql软件目录：执行命令 cd /usr/local/mysql
    修改当前目录拥有者为mysql用户：执行命令 chown -R mysql:mysql ./
    安装数据库：执行命令 ./scripts/mysql_install_db --user=mysql
    修改当前目录拥有者为root用户：执行命令 chown -R root:root ./
    修改当前data目录拥有者为mysql用户：执行命令 chown -R mysql:mysql data
    ```

    * 启动 

  * 添加开机启动：执行命令cp support-files/mysql.server /etc/init.d/mysql，把启动脚本放到开机初始化目录
  * 启动mysql服务：执行命令service mysql start

    * 提示没有 xxx 数据库文件时候

      ```
      mkdir -p /var/log/mariadb/
      cd /var/log/mariadb/
      touch mariadb.log
      chmod -R 775 mariadb.log
      chown -R mysql:mysql mariadb.log
      ```

  * 修改密码：./bin/mysqladmin -u root password '密码'

  * 执行 mysql

    * 没有的话就添加软链接：ln -s /usr/local/mysql/bin/mysql /usr/local/bin/mysql
    * 报 /tmp/mysql.sock 找不到的话
      * 查找mysql.sock ：find / -name mysql.sock
      * 添加软链接：ln -s /var/lib/mysql/mysql.sock /tmp/

* 查看表结构
    desc tabl\_name;
* 递增设置
    alter table pt\_operation\_log modify id bigint\(20\) auto\_increment;



