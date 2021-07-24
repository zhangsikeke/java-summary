# docker常用工具安装

## 搭建Portainer可视化界面

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
　　--name prtainer-test \　　　# 指定容器名
　　--restart=always \　　　　  # 设置自动启动
    -v /opt/portainer:/data \    # 保存portainer数据到宿主机
    -v /var/run/docker.sock:/var/run/docker.sock \　　# 单机方式必须指定
　　portainer/portainer
#集群模式，可添加多节点，功能得到扩展，较为全面。
docker service create \
　　--name Portainer \　　　　　　# 指定服务器
　　--publish 9000:9000 \　　　　# 端口映射
　　--constraint 'node.role == manager' \　　　# 限制仅在管理节点运行
　　--mount type=bind,src=//opt/portainer,dst=/data \　　# 挂载portainer数据到宿主机
　　--mount type=bind,src=//var/run/docker.sock,dst=/var/run/docker.sock \ # 指定docker.sock
　　portainer/portainer \
　　-H unix:///var/run/docker.sock
```

## Portainer添加Docker节点

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

## rancher安装

- 官方文档https://rancher.com/docs/rancher/v1.6/zh/
- stable稳定版本在生产环境中使用的版本
-  最好将rancher数据挂载到外部，这样即使删除rancher再重新创建rancher容器，数据也不会丢失 

```shell
mkdir -p /home/data/rancher
docker pull rancher/server:stable　
docker run -d -p 8088:8080 --restart=always -v /home/data/rancher:/var/lib/mysql rancher/server:stable
#修改参数restart
docker containerID/name update --restart=always
```

1.  系统管理-》访问控制-》启动访问控制并设置用户名和密码
2.  添加主机 选其他地址 填写本机IP：http://192.168.10.17:8089
3. 第4步填写主机地址192.168.10
4. 第5步骤按照提示操作





