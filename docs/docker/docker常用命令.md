# docker常用命令

## docker常用命令

```shell
#查看docker版本信息
dokcer version 
#查看docker信息
docker info 
#启动docker
systemctl start docker
#停止docker服务
systemctl stop docker

#修改etc/docker/daemon.json里的远程仓库后执行下面使之生效
systemctl daemon-reload 
systemctl restart docker
```

## 操作镜像

```shell
#在仓库查找docker镜像
docker search <image name>
#从仓库拉取
docker pull <image name>
#查看本地docker镜像
docker images
#删除镜像
docker rmi 镜像id
#强制删除镜像
docker rmi -f 镜像id
#查看docker进程
docker ps -a
#清理dangling images
docker image prune
```

## 操作容器

```shell
#查询容器
docker container ls -a
#启动容器
docker start 容器id
#重启容器
docker restart 容器id
#停止容器
docker stop 容器id
#如果你要删除的 container 还是运行状态，那么就要先把容器停止了：
docker container stop <container ID>
#找到需要删除的容器对应的  container ID 或者名字,进行删除:
docker container stop $(docker container ls -a -q)
#批量删除容器:
docker container rm $(docker container ls -a -q)
#删除单个容器
docker rm 容器id

#查看容器内控制台打印，只读一次
docker logs 容器id
#查看容器内控制台打印监控模式
docker logs -f 容器id
#查看容器端口
docker port 7a38a1ad55c6
#容器重命名
docker rename 旧名 新名字
#修改参数restart
docker containerName update --restart=always
```

## 运行一个docker hello-world

```
docker searh hello-world 
docker pull hello-world
docker images
docker run hello-world
```

## 构建docker iamge

1. 编写Dockerfile

   ```shell
   FROM java:8
   #WORKDIR指令用于指定容器的一个目录， 容器启动时执行的命令会在该目录下执行。
   WORKDIR /
   #挂载到/tmp目录,它将一个文件夹从主机文件系统挂载到容器中。容器对挂载文件夹执行的任何修改都将在主机文件系统上持久存在。当容器被替换时，当新容器启动时，文件夹将重新挂载.
   VOLUME /tmp
   #将当前docker-hello-world-0.0.1-SNAPSHOT.jar 复制到容器根目录下
   ADD docker-hello-world-0.0.1-SNAPSHOT.jar app.jar
   RUN bash -c 'touch /app.jar'
   ENTRYPOINT ["java","-jar","/app.jar"]
   ```

2. 构建image

   ```shell
   # . 表示当前目录 -f 参数指定Dockerfile文件  -t 表示 制作的镜像tag  "注意后面有个."
   # :后面一般表示版本号
   docker build -f DockerfileServer  -t  zhangsike/helloworld-app:1.0.0 .
   docker build -t  zhansike/web:1.0 .
   ```

3. 创建容器

   ```shell
   #注意端口号顺序，第一个8080是指在doucker暴露在外部的端口，8080是指docker里面的项目的端口
   #-d：让容器在后台启动
   docker run -d -p 8080:8080 zhansike/web:1.0
   ```



