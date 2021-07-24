# jenkins+docker构建流水线

## 安装docker

```shell
#安装依赖（docker依赖于系统的一些必要的工具，可以提前安装）
yum install -y yum-utils device-mapper-persistent-data lvm2
#添加软件源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#安装docke-ce
yum list docker-ce --showduplicates | sort -r #查看所有docker-ce版本
yum install docker-ce-18.06.3.ce
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
docker ps 查看当前正在运行的容器
docker ps -a 查看所有容器的状态
docker start/stop id/name 启动/停止某个容器
docker attach id 进入某个容器(使用exit退出后容器也跟着停止运行)
docker exec -ti id 启动一个伪终端以交互式的方式进入某个容器（使用exit退出后容器不停止运行）
docker images 查看本地镜像
docker rm id/name 删除某个容器
docker rmi id/name 删除某个镜像
docker run --name test -ti ubuntu /bin/bash  复制ubuntu容器并且重命名为test且运行，然后以伪终端交互式方式进入容器，运行bash
docker build -t soar/centos:7.1 .  通过当前目录下的Dockerfile创建一个名为soar/centos:7.1的镜像
docker run -d -p 2222:22 --name test soar/centos:7.1  以镜像soar/centos:7.1创建名为test的容器，并以后台模式运行，并做端口映射到宿主机2222端口，P参数重启容器宿主机端口会发生改变
```



## 安装java环境

```shell
#安装java
#如果系统中已经安装了jdk，需要先卸载
rpm -qa grep | java #查找
rpm -e --nodeps java-1.6.0xxxxxx  #卸载
java -version


1.在线安装
yum install -y java       #安装jre
yum install -y java-devel #原生安装,包含JDK，https://www.bfshu.com/bug/185

2.本地安装
1.下载linux jdk版本
	"最新版本下载地址：http://www.oracle.com/technetwork/java/javase/downloads/index.html"
	"历史版本下载地址：　　http://www.oracle.com/technetwork/java/javase/archive-139210.html"
2.上传解压
	mkdir -p /user/local/java
	tar zxvf jdk-8u161-linux-x64.tar.gz -C /user/local/java
3.安装jdk依赖环境glibc，不安装后期容易出问题
	安装命令 yum install glibc.i686
	"报错cannot file file /usr/local/jdk1.7.0_79/lib/java......"
	"注：出现error 一个.so文件not found （未找到）那可能你的Linux版本  glibc版本还未达到2.4"
	"原因分析:linux 上安装jdk 1.7版本必须要配合  glibc 核心源码版本在2.4或以上"
4.配置环境
	vim  /etc/profile
	#最后加入
	export JAVA_HOME=/usr/local/java/jdk1.8.0_161
	export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	export PATH=$PATH:$JAVA_HOME/bin
	#加载配置
	source /etc/profile


#如果识别不到java建立软连接
ln -s /usr/local/jdk1.8.0_121/jdk/bin/java /usr/bin/java
```

## 安装jenkins

```shell
1.添加Jenkins库到yum库，Jenkins将从这里下载安装。
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins
2.如果不能安装就到官网下载jenkis的rmp包，官网地址（http://pkg.jenkins-ci.org/redhat-stable/）
wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
rpm -ivh jenkins-2.7.3-1.1.noarch.rpm

#配置jenkis的端口
vi /etc/sysconfig/jenkins
#启动jenkins
service jenkins start/stop/restart

#安装成功后Jenkins将作为一个守护进程随系统启动
#系统会创建一个“jenkins”用户来允许这个服务，如果改变服务所有者，同时需要修改/var/log/jenkins, #/var/lib/jenkins, 和/var/cache/jenkins的所有者
#启动的时候将从/etc/sysconfig/jenkins获取配置参数
#默认情况下，Jenkins运行在8080端口，在浏览器中直接访问该端进行服务配置
#Jenkins的RPM仓库配置被加到/etc/yum.repos.d/jenkins.repo
#初始密码在：/var/lib/jenkins/secrets/initialAdminPassword 
#安装maven插件 Maven Integration

"常见问题1"
#如果缺少对应权限将jenkins用户加入对应用户组，否者构建时会报权限不足无法执行
gpasswd -a jenkins root #将用户jenkins加入到root组
gpasswd -a jenkins dockr #将用户jenkins加入到root组

"常见问题2"
#构建maven项目时报找不到java环境，但在终端上java环境是可见的，解决办法
#1.简历软连接
ln -s /usr/local/jdk1.8.0_121/jdk/bin/java /usr/bin/java
#2.在jenkins服务中配置java环境变量
vim /etc/init.d/jenkins
#将正确的java路径填写到下面，重启服务
candidates="
/etc/alternatives/java
/usr/lib/jvm/java-1.8.0/bin/java
/usr/lib/jvm/jre-1.8.0/bin/java
/usr/lib/jvm/java-1.7.0/bin/java
/usr/lib/jvm/jre-1.7.0/bin/java
/usr/lib/jvm/java-11.0/bin/java
/usr/lib/jvm/jre-11.0/bin/java
/usr/lib/jvm/java-11-openjdk-amd64
/usr/bin/java
"

systemctl restart jenkins
```

## 安装maven

```shell
wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.1.1/binaries/apache-maven-3.1.1-bin.tar.gz
tar -zxvf  apache-maven-3.1.1-bin.tar.gz
设置环境变量
vi /etc/profile 
#maven 安装的路径
export M2_HOME=/maven/maven
export PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin
#使环境变量生效
source /etc/profile
mvn -version
```

## 配置jenkins项目

```shell
#手动安装插件下载地址http://updates.jenkins-ci.org/download/plugins/
#如果在线找找不到插件，从上面的下载地址 ctrl+f查找的插件下载后通过插件管理中心高级上传安装
1.jenkins安装maven插件
	系统管理->插件管理->可选插件->Maven Integration plugin
 2.jenkins安装Publish over SSH
	系统管理->插件管理->可选插件->Publish over SSH
3.新建任务-》构建一个maven风格的项目
	gengeral      ->丢弃旧的构建/保留3天
	构建触发器      ->轮询SCM/日程表 *****（每分钟查询构建）
	源码Subversion ->Repository URL填写项目svn
	build         ->Root POM/填pom.xml,
					Goals and options填clean package -Dmaven.test.skip=true
	Post Steps ->执行shell
	#停止容器-》删除容器-》删除镜像-》新建容器
	containerName=springboot-demo
    docker stop $containerName
    docker rm $containerName
    docker rmi $containerName
    \cp target/springboot-restful-0.0.1-SNAPSHOT.jar springboot-restful-0.0.1-SNAPSHOT.jar
    docker build -t springboot-demo .
    docker run -d -p 8081:8080 --name springboot-demo -v /data/springboot-demo:/data/springboot-demo springboot-demo:latest
	#注：\cp 表示复制时不提示直接覆盖
	
    #注意将Dockerfile放在maven项目根目录
    #Dockerfile内容：
    FROM java:8
    MAINTAINER zincredible zincredible@126.com
    WORKDIR /
    VOLUME /tmp
    ADD springboot-restful-0.0.1-SNAPSHOT.jar app.jar
    RUN bash -c 'touch /app.jar'
    ENTRYPOINT ["java","-jar","/app.jar"]
```

## jenkins通过ssh构建发布docker节点镜像

1.  配置publish over SSH

   ```shell
   系统设置-》系统配置-》publish over ssh-》SSH Server
   Name 192.168.10.16-Node
   Hostname 192.168.10.16
   Username root
   Remote Directory /home
   高级选择 Use password authentication, or use a different key #输入密码
   ```

2. 在项目post step中发送jar包到节点服务器构建镜像并启动

   ```shell
   Name 192.168.10.16-Node
   Source files	target/*.jar
   Remove prefix	target
   Remote directory	springboot/ #远程目录，例如SSH配置的远程目录为/home,那么这个目录会添加到/home后
   Exec command
   
   Name 192.168.10.16-Node
   Source files	Dockerfile
   Remove prefix	
   Remote directory	springboot/ 
   Exec command
   #远程服务器上执行的命令
   docker stop springboot-demo
   docker rm -v springboot-demo  # -v删除数据卷volume
   docker rmi springboot-demo
   cd /home/springboot
   docker build -t springboot-demo .
   docker run -d -p 8081:8080 --name springboot-demo -v /data/springboot-demo:/data/springboot-demo springboot-demo:latest
   ```

## 例子

- 建立5个springCloud项目,准备2台以上服务器，我准备了master和node两台服务器，

  1. 所有服务器均安装docker，

  2. jenkins portainer按转发在master节点上，

  3. eureka和zuul网关部署在master节点上，

  4. springboot-service-a/springboot-service-b/springboot-service-c部署在node节点上

  5. 网关作为同一的访问入口，springboot-service-b通过feign组件调用springboot-service-a的方法

- 工程说明，完整demo见https://gitee.com/zhangsike/springboot-learn中以下5个项目

  1. spring-eureka-server 注册中心，端口7001

     ```shell
     # jenkins Post Steps 启动脚本配置
     prjName=spring-eureka-server
     if docker ps -a | grep -i $prjName; then
         docker stop $prjName
     	docker rm -v $prjName
     fi
     if docker images | grep -i $prjName; then
     	docker rmi -f $prjName
     fi
\cp 'target/'$prjName-'0.0.1-SNAPSHOT.jar' $prjName'-0.0.1-SNAPSHOT.jar'
     docker build -t $prjName .
docker run -d --expose=7001 -p 7001:7001 --name $prjName $prjName':latest' --server.port=7001
     ```
  
    2. spring-cloud-zuul 网关，端口7000
  
       ```shell
       # jenkins Post Steps 启动脚本配置
       prjName=spring-cloud-zuul
       if docker ps -a | grep -i $prjName; then
           docker stop $prjName
       	docker rm -v -f $prjName
  fi
       if docker images | grep -i $prjName; then
  	docker rmi -f $prjName
       fi
       \cp 'target/'$prjName-'0.0.1-SNAPSHOT.jar' $prjName'-0.0.1-SNAPSHOT.jar'
       docker build -t $prjName .
       docker run -d --expose=7000 -p 7000:7000 --name $prjName $prjName':latest' --server.port=7001
       ```
  
    3. springboot-service-a 服务a，端口7005
  
       ```shell
       # jenkins Post Steps 通过jenkins publish over ssh 插件发送jar包和Dockerfile到远程服务器，远程执行docker构建镜像脚本和运行命令试实现部署
       
       Transfers	
        	Source files	target/*.jar
        	Remote directory	springboot/springboot-service-b/
       
       Transfers
        	Source files Dockerfile
        	Remote directory   springboot/springboot-service-b/
        	Exec command	
          prjName=springboot-service-b
               if docker ps -a | grep -i $prjName; then
       ```
       
       ```shell
       docker stop $prjName
                 docker rm -v $prjName
             fi
             if docker images | grep -i $prjName; then
                 docker rmi -f $prjName
             fi
                cd '/home/springboot/'$prjName
                docker build -t $prjName .
                docker run -d --expose=7006 -p 7006:7006 --name $prjName $prjName':latest' --server.port=7006
       ```
       
       
       
    4. springboot-service-b 服务b，端口7006，部署shell和springboot-service-a一样
  
    5. springboot-service-c 服务c，端口7007，部署shell和springboot-service-a一样





