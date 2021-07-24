# linux下常用软件安装

###### 安装java

```shell
#安装java
#如果系统中已经安装了jdk，需要先卸载
rpm -qa grep | java #查找
rpm -e --nodeps java-1.6.0xxxxxx  #卸载
java -version

#在线安装
yum install -y java       #安装jre
yum install -y java-devel #原生安装,包含JDK，https://www.bfshu.com/bug/185

#如果识别不到java建立软连接
ln -s /usr/local/jdk1.8.0_121/jdk/bin/java /usr/bin/java

#本地安装
1.下载linux jdk版本
	"最新版本下载地址：http://www.oracle.com/technetwork/java/javase/downloads/index.html"
	"历史版本下载地址：http://www.oracle.com/technetwork/java/javase/archive-139210.html"
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


```

###### 安装jenkins

```shell
#添加Jenkins库到yum库，Jenkins将从这里下载安装。
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins
#如果不能安装就到官网下载jenkis的rmp包，官网地址（http://pkg.jenkins-ci.org/redhat-stable/）
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
#1.建立软连接
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
#忘记密码处理
修改 /var/lib/jenkins.config.xml useSecurity为false后重启

systemctl restart jenkins
```

###### 安装maven

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
```