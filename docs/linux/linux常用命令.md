[TOC]



###### 1.linux常用命令

```shell
passwd root #修改root用户密码
gpasswd -a cold root #将用户cold加入到root组
gpasswd -a jenkins docker
gpasswd -d cold root #将用户cold从root组中移出
groups 查看当前所属组

#以用户名root连接服务器
ssh root@114.114.112.123
#以字节显示列表
ls -l --block-size k 
ls -hl
ls -l

ps -aux
#查看系统版本
uname -a 
#端口被谁占用
netstat -tlnp|grep 8080
#查看某个进程CPU，内存占用
top -p PID
#查看java进程的PID
ps -ef | grep java
#查看java进程情况
ps -aux | grep java
#查看更详细的内存占比
cat /proc/1042/status 
#终止PID为1402的进程
kill 1402

#查看防火墙
firewall-cmd --state         
#停止/开启firewall 
systemctl stop/start firewalld.service    
#禁止/打开firewall开机启动
systemctl disable/enable firewalld.service
```

###### 2.服务操作

```
systemctl start firewalld.service 启动防火墙服务
systemctl stop firewalld.service  关闭防火墙服务
systemctl restart firewalld.service 重启防火墙服务

systemctl status firewalld.service 显示防火墙服务状态：
systemctl enable firewalld.service 在开机时启用防火墙服务
systemctl disable firewalld.service 在开机时禁用防火墙服务

systemctl list-unit-files | grep firewalld.service 查看防火墙服务服
systemctl is-enabled firewalld.service 查看防火墙服务是否开机启动：
systemctl list-unit-files|grep enabled 查看已启动的服务列表：
systemctl --failed 查看启动失败的服务列表：
systemctl list-units --type=service --all 查看服务列表
```

###### 3.文件操作

```shell
touch 创建文件
mkdir                创建文件夹
-Z：设置安全上下文，当使用SELinux时有效；
-m<目标属性>或--mode<目标属性>建立目录的同时设置目录的权限；
-p或--parents 若所要建立目录的上层目录目前尚未建立，则会一并建立上层目录；
--version 显示版本信息

rm
-d：直接把欲删除的目录的硬连接数据删除成0，删除该目录；
-f：强制删除文件或目录；
-i：删除已有文件或目录之前先询问用户；
-r或-R：递归处理，将指定目录下的所有文件与子目录一并处理；
--preserve-root：不对根目录进行递归操作；
-v：显示指令的详细执行过程。 
```

###### 4.防火墙操作

```shell
systemctl start firewalld.service 启动防火墙服务
systemctl stop firewalld.service  关闭防火墙服务
systemctl restart firewalld.service 重启防火墙服务

firewall-cmd --version 查看版本
firewall-cmd --help 查看帮助
firewall-cmd --state 显示状态
firewall-cmd --zone=public --list-ports 查看所有打开的端口
firewall-cmd --reload 更新防火墙规则
firewall-cmd --get-active-zones 查看区域信息
firewall-cmd --get-zone-of-interface=eth0 查看指定接口所属区域
firewall-cmd --panic-on 拒绝所有包
firewall-cmd --panic-off 取消拒绝状态 
firewall-cmd --query-panic 查看是否拒绝

配置firewalld-cmd
开启一个端口
添加
firewall-cmd --zone=public --add-port=80/tcp --permanent 
（--permanent永久生效，没有此参数重启后失效）
重新载入
firewall-cmd --reload
查看
firewall-cmd --zone= public --query-port=80/tcp
删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent
```

###### 5.ssh服务检查

```shell
#检查ssh是否安装
yum list installed | grep openssh-server
#出现以下信息说明已经安装
[root@localhost ~]# yum list installed | grep openssh-server
Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
openssh-server.x86_64                7.4p1-16.el7                   @anaconda

#如果没有ssh需要安装，如果有跳过此步骤
yum install openssh-serve

修改ssh配置
vim /etc/ssh/sshd_config

#将监听端口和端口地址打开
Port 22
#AddressFamily any
ListenAddress 0.0.0.0
ListenAddress ::
#开启允许root用户登录权限
#LoginGraceTime 2m
PermitRootLogin yes
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10
#最后，开启使用用户名密码来作为连接验证
#PasswordAuthentication yes
#PermitEmptyPasswords no
PasswordAuthentication yes

#重启ssh服务
systemctl restart sshd.service
```

###### 6.NAT模式下修改固定地址

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
#BOOTPROTO=dhcp
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=23925c31-1d91-4e5c-8afa-2893e41c8147
DEVICE=ens33
ONBOOT=yes
DNS1=114.114.114.114
IPADDR=192.168.159.130
NETMASK=255.255.255.0
GATEWAY=192.168.159.2



systemctl restart network

#DNS1=114.114.114.114    #DNS
#IPADDR=192.168.183.130  #设置一个和原来动态分配的ip在同一子网的IP
#NETMASK=255.255.255.0   #子网掩码
#GATEWAY=192.168.183.2   #网关，vmvare的nat模式网关为x.x.x.2
```

