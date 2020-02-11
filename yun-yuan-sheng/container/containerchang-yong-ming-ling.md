## 镜像

1. 列出本机所有 image 文件
   ```
   docker image ls
   ```
2. 删除 image 文件
   ```
   docker image rm [imageName]
   ```
3. 镜像拉取
   ```
   docker image pull hello-world
   ```

## 查看容器

1. 列出当前所有正在运行的container
   ```
   docker ps
   ```
2. 列出所有container
   ```
   docker ps -a
   ```
3. 列出最近一次启动的container

   ```
   docker ps -l
   ```

## 操作容器

1. 删除容器
   ```
   docker rm Name/ID
   ```
2. 删除所有容器
   ```
   docker rm `docker ps -a -q`
   ```
3. 停止、启动、杀死一个容器
   ```
   docker stop Name/ID  
   docker start Name/ID 
   docker kill Name/ID
   ```
4. 容器日志查看
   ```
   docker logs Name/ID  
   ```










