[TOC]

# docker安装

## 一、安装

### 1.安装

```shell
#docker安装
#======================================================
#安装依赖（docker依赖于系统的一些必要的工具，可以提前安装）
yum install -y yum-utils device-mapper-persistent-data lvm2
#添加软件源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#安装docke-ce
yum list docker-ce --showduplicates | sort -r #查看所有docker-ce版本
yum install -ysyst	 docker-ce-18.06.3.ce
```

### 2.启动服务

```sh
systemctl start/stop docker
systemctl enable/disable docker #开机启动
```

### 3.配置镜像加速

```shell
#配置docker加速镜像
/etc/docker/daemon.json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
#使配置生效
systemctl daemon-reload 
systemctl restart docker
```

### 4.常用几个命令

```shell
#常用的几个命令

docker search <image name> #在仓库查找docker镜像
docker pull <image name> #从仓库拉取
docker images #查看本地docker镜像
docker rmi 镜像id #删除镜像
docker rmi -f 镜像id #强制删除镜像

docker container ls #查看当前正在运行的容器
docker container ls -a #查看所有容器
docker start/stop id/name #启动/停止某个容器
docker rm -v id/name #删除某个容器 -v删除volume -f强制删除

#查看docker进程
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

## 二、开启远程访问

```shell
#开启docker远程访问
#======================================================
vim /lib/systemd/system/docker.service
#修改 ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
#注意开启防火墙
systemctl daemon-reload
service docker restart
curl http://ip:2375/version #有信息表示成功
```

