# docker私有镜像仓库搭建

```shell
1. 拉取镜像
docker pull registry

2. 创建容器
#推荐配置跨域创建容器
#创建文件/home/app/config.yml
vim /home/app/config.yml
#config.yml内容
version: 0.1
log:
  fields:
    service: registry
storage:
  delete:
  	enabled: true
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
    Access-Control-Allow-Origin: ['*']
    Access-Control-Allow-Methods: ['*']
    Access-Control-Max-Age: [1728000]
    Access-Control-Expose-Headers: ['Docker-Content-Digest']
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
#运行
docker run -d -p 5000:5000 \
--restart=always \
-v /home/app/config.yml:/etc/docker/registry/config.yml \
-v /data/images/registry:/var/lib/registry \
--name my-registry \
registry

#普通安装
docker run -d -p 5000:5000 \
--restart=always \
-v /data/images/registry:/var/lib/registry \
--name my-registry \
registry
    
3. 查看
#在客户端查看镜像仓库中的所有镜像
curl http://192.168.10.17:5000/v2/_catalog
#查看tag（或版本）
curl http:ip:5000/v2/your-image-name/tags/list

4. 安装可视化UI docker-registry-ui
#docker镜像官网https://hub.docker.com/
#github https://github.com/Joxit/docker-registry-ui
docker run -d -p 5001:80 --restart=always joxit/docker-registry-ui
#如果访问报错
#An error occured: Check your connection and your registry must have `Access-Control-Allow-Origin` header set to `http://192.168.10.17:5001`
解决方法：https://www.cnblogs.com/huangxincheng/p/11131623.html

5. 上传镜像
#打tag
docker tag your-image-name:tagname ip:5000/your-image-name:tagname
docker tag spring-eureka-server:latest 192.168.10.17:5000/spring-eureka-server:latest
#上传
docker push 192.168.10.17:5000/spring-eureka-server:latest
#报错如下
"The push refers to repository [192.168.10.17:5000/spring-eureka-server]Get https://192.168.10.17:5000/v2/: http: server gave HTTP response to HTTPS client,https的client不接受http的response"
解决方法:
https://www.cnblogs.com/huangxincheng/p/11131623.html,
https://docs.docker.com/registry/insecure/,
在/etc/docker/daemon.json中加入 "insecure-registries" : ["192.168.10.17:5000"]"
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"],
  "insecure-registries" : ["192.168.10.17:5000"]
}
systemctl daemon-reload 
systemctl restart docker
#再次上传即可
#拉镜像
docker pull ip:5000/your-image-name:tagname
```

jenkins构建镜像上传到镜像仓库

```
1. 将jar包和Dockerfile复制到同一个目录中
2. 打镜像
3. 给镜像打标签
4. 将镜像推送到镜像仓库
host=192.168.10.17:5000
prjName=springboot-service-c
\cp target/${prjName}-0.0.1-SNAPSHOT.jar ${prjName}-0.0.1-SNAPSHOT.jar
docker build -t ${prjName}:${BUILD_ID} .
docker tag ${prjName}:${BUILD_ID} ${host}/${prjName}:${BUILD_ID}
docker push ${host}/${prjName}:${BUILD_ID}
```

使用 hyper/docker-registry-web搭建可视化