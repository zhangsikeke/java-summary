#### 一般命令

```shell
sudo apt-get install  vim/openssh-server #安装软件
sudo ufw status/disable/enable #防火墙 状态/开启/关闭
sudo ps -ef | grep ssh #查询ssh服务启动状态
sudo service ssh status #查询ssh服务启动状态

sudo passwd #设置root密码
sudo passwd root #修改root用户密码

#==================用户操作==============================
id username #查看用户信息
userdel -r 用户名 #删除用户的同时，删除用户的主目录，以释放硬盘空间
sudo useradd username -d /home/username -m #-d /home/username 用户登录主目录 -m不存在则创建
cat /etc/passwd | grep username #查看新建用户
sudo  passwd userName #设置密码

#==================给用户sudo权限==============================
sudo chmod u+w /etc/sudoers #给用户增加sudo权限
sudo vim /etc/sudoers #编辑权限文件
# 在root ALL =(ALL:ALL) 添加一行
username ALL=(ALL:ALL) ALL
# 恢复文件权限
sudo chmod u-w /etc/sudoers
# 解决远程用户登录问题
sudo vim /etc/passwd
#在最后追加一行 :/bin/bash
demo: username:x1001:1001::/home/username:/bin/bash

sudo chown username file  #修改文件归属
sudo chown username:groupname file #修改文件归属和组

# 开启远程登录
sudo vim /etc/passwd #追加/bin/bash，例如username:x1001:1001::/home/yaojunma、:/bin/bash
sudo useradd yaojunma -d /home/yaojunma -m
sudo passwd yaojunma

ps -aux | grep code-server
#获取pid并结束
#!/bin/sh
codeServerPid=`ps -ef|grep code-server|grep -v grep|awk '{print $2}'`
kill -9 $codeServerPid
```

#### 卸载程序

```shell
dpkg --list | grep 程序名称
sudo apt-get --purge remove 程序名称 #--purge卸载配置

dpkg --list #系统已安装软件
sudo apt-get --purge remove 包名 #删除软件和配置，--purge可选表示删除配置
sudo apt-get update 获取到最新的软件包
apt-cache search package 搜索包
apt-cache show package 获取包的相关信息，如说明、大小、版本等
sudo apt-get install package 安装包
sudo apt-get install package - - reinstall 重新安装包
sudo apt-get -f install 强制安装
sudo apt-get remove package 删除包
sudo apt-get --purge remove  package  删除包，包括删除配置文件等
sudo apt-get autoremove 自动删除不需要的包
sudo apt-get update 更新源
sudo apt-get upgrade 更新已安装的包
sudo apt-get dist-upgrade 升级系统
sudo apt-get dselect-upgrade 使用 dselect 升级
apt-cache depends package 了解使用依赖 
apt-cache rdepends package 了解某个具体的依赖
sudo apt-get build-dep package 安装相关的编译环境
apt-get source package 下载该包的源代码
sudo apt-get clean && sudo apt-get autoclean 清理下载文件的存档
sudo apt-get check 检查是否有损坏的依赖
```

#### 安装code-server

```shell
#ubantu code-server安装
curl -fOL https://github.com/cdr/code-server/releases/download/v3.5.0/code-server_3.5.0_amd64.deb
sudo dpkg -i code-server_3.5.0_amd64.deb
sudo systemctl enable --now code-server@$USER #$USER表示当前用户

#code-server 已配置成服务，跟随系统自动启动
systemctl start code-server@markguo
systemctl stop code-server@markguo
systemctl restart code-server@markguo
#配置文件-改密码端口等，修改配置文件需要重启,远程访问需要添加host: 0.0.0.0
/home/markguo/.config/code-server/config.yaml
```





