###### docker安装

```shell
#安装依赖（docker依赖于系统的一些必要的工具，可以提前安装）
yum install -y yum-utils device-mapper-persistent-data lvm2
#添加软件源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#安装docke-ce
yum list docker-ce --showduplicates | sort -r #查看所有docker-ce版本
yum install -ysyst	 docker-ce-18.06.3.ce
#启动服务
systemctl start/stop docker
systemctl enable/disable docker #开机启动

#配置docker加速镜像
/etc/docker/daemon.json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
#使配置生效
systemctl daemon-reload 
systemctl restart docker

#常用的几个命令
docker exec -it 容器名或id /bin/sh  #在容器内部执行命令
docker images #查看镜像
docker ps #查看当前正在运行的容器
docker ps -a #查看所有容器的状态
docker start/stop id/name #启动/停止某个容器
docker attach id #进入某个容器(使用exit退出后容器也跟着停止运行)
docker exec -ti id #启动一个伪终端以交互式的方式进入某个容器（使用exit退出后容器不停止运行）
docker images #查看本地镜像
docker rm -v id/name #删除某个容器 -v删除volume -f强制删除
docker rmi id/name #删除某个镜像
docker run --name test -ti ubuntu /bin/bash  #复制ubuntu容器并且重命名为test且运行，然后以伪终端交互式方式进入容器，运行bash
docker build -t soar/centos:7.1 .  #通过当前目录下的Dockerfile创建一个名为soar/centos:7.1的镜像
docker run -d -p 2222:22 --name test soar/centos:7.1  #以镜像soar/centos:7.1创建名为test的容器，并以后台模式运行，并做端口映射到宿主机2222端口，P参数重启容器宿主机端口会发生改变
docker volume ls	#List volumes
docker volume prune	#Remove all unused local volumes
```



###### docker常用命令

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

###### 运行一个docker hello-world

```
docker searh hello-world 
docker pull hello-world
docker images
docker run hello-world
```

###### 构建docker iamge

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

操作镜像

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

操作容器

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
```

###### 搭建Portainer可视化界面

```shell
#原文链接：https://blog.csdn.net/u011781521/article/details/80469804
docker search portainer
docker pull portainer/portainer
#启动
docker run -d -p 9000:9000 \
    --restart=always \
    -v /opt/portainer:/data/portainer \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --name prtainer-test \
    portainer/portainer

#启动完成后通过9000端口访问http://192.168.159.130:9000
#设置用户名密码 admin admin1234

#单机模式，最为简单，运行模式需要选择local。
docker run -d \　　　　　　　　# 后台运行容器
　　-p 9000:9000 \　　　　　　# 默认9000端口，映射到宿主机，通过本地地址访问
　　--name portainer \　　　# 指定容器名
　　--restart=always \　　　　  # 设置自动启动
    -v /opt/portainer:/data \    # 保存portainer数据到宿主机
    -v /var/run/docker.sock:/var/run/docker.sock \　　# 单机方式必须指定
　　portainer/portainer

docker run -d -p 9000:9000 --name portainer --restart=always -v /opt/portainer:/data/portainer  -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer

#集群模式，可添加多节点，功能得到扩展，较为全面。
docker service create \
　　--name Portainer \　　　　　　# 指定服务器
　　--publish 9000:9000 \　　　　# 端口映射
　　--constraint 'node.role == manager' \　　　# 限制仅在管理节点运行
　　--mount type=bind,src=//opt/portainer,dst=/data \　　# 挂载portainer数据到宿主机
　　--mount type=bind,src=//var/run/docker.sock,dst=/var/run/docker.sock \ # 指定docker.sock
　　portainer/portainer \
　　-H unix:///var/run/docker.sock
　　
docker run -d -p 9000:9000 --restart=always --name portainer -v /opt/portainer:/data/portainer   portainer/portainer

docker run -d -p 8081:8080 --name springboot0demo -v /data/springboot-demo:/data/springboot-demo springboot-demo:latest
```

###### docker+rancher构建部署流水线

- 安装rancher

```shell
docker search rancher
docker pull rancher/server:stable　
docker run -d -p 8089:8080 rancher/server:stable #stable稳定版本在生产环境中使用的版本
docker run -d --restart=always -p 8089:8080 rancher/server:stable
#修改参数restart
docker container update --restart=always
```

1.  系统管理-》访问控制-》启动访问控制并设置用户名和密码
2.  添加主机 选其他地址 填写本机IP：http://192.168.10.17:8089
3. 第4步填写主机地址192.168.10
4. 第5步骤按照提示操作

###### Protainer添加Docker节点

1. 分别在node与master上安装docker。另，多台node以此类推,在node上开启2375端口

   ```shell
   vim /lib/systemd/system/docker.service
   #修改 ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
   curl http://localhost:2375/version 有信息表示成功
   #注意开启防火墙
   systemctl daemon-reload
   service docker restart
   ```

2. 在portainer上添加endpoint

   ```shell
   #检测节点是否可用
   docker -H 192.168.10.16 info
   #最后在Portainer->endPoints->add endpoint->Docker添加即可
   ```



