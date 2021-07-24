[TOC]

## 一、git安装

```shell
#安装教程
https://blog.csdn.net/weixin_44950987/article/details/102619708
#1.初始化git配置SSH密钥
git config --global user.name "你的提交名"
git config --global user.email "你的email"
ssh-keygen -t rsa -C "你的email" 连续按2次回车
git config --list
cd ~/.ssh
#id_rsa和id_rsa.pub
#把id_rsa.pub复制到git服务器里或者gitee/github

#2.TortoiseGit安装教程
#全部默认安装
#git可设置edit global .gitconfig将最后一行 
#[credential]
	helper = manager
#修改为
	helper = store
#这样修改用户名和邮箱可保存,就可以不用每次都输入账号了
#Network设置为ssh客户端D:\Program Files (x86)\Git\usr\bin\ssh.exe，注意不是D:\Program Files (x86)\Git\bin\sh.exe本人疏忽修改错误，导致认证失败各种找不到原因，原来是误设置成bin下的sh.exe,实际ssh客户端在user\bin\ssh.exe
```



## 二、git常用命令

 ### 1.查看公钥

```
cd ~/.ssh 进入ssh目录查看公钥
```

```shell
git clone git@gitee.com:zhangsike/java-leaning.git
git clone -b 分支名 git@gitee.com:zhangsike/java-leaning.git
```

 ### 2.分支切换

```shell
git branch 查看分支的情况，前面带*号的就是当前分支
git branch 分支名 创建分支
git checkout 分支名 切换当前分支到指定分支
git checkout  -b 分支名 创建分支并切换到创建的分支
git merge 分支名  合并某分支的内容到当前分支
git branch -d 分支名 删除分支 初始化：创建一个git仓库，创建之后就会在当前目录生成一个.git的文件
```

 ### 3.提交

```shell
git init 初始化：创建一个git仓库，创建之后就会在当前目录生成一个.git的文件
git add filename 添加文件：把文件添加到缓冲区
git add .
git add --all 添加所有文件到缓冲区（从目前掌握的水平看，和后面加“.”的区别在于，加all可以添加被手动删除的文件，而加“.”不行
git rm filename 删除文 
git commit -m "提交的说明" 提交：提交缓冲区的所有修改到仓库(注意：如果修改了文件但是没有add到缓冲区，也是不会被提交的)
```

 ### 4.更新

```shell
git pull 从远端库更新内容到本地
```



 ### 5.推送

```shell
git push -u origin master 将本地仓库内容推送到远端仓库(-u 表示第一次推送master分支的所有内容，后面再推送就不需要-u了)，跟commit的区别在于一个是提交到本地仓库，一个是提交到远程仓库
```

### 6.fork分支和上游源同步

```shell
#====================初次同步====================
# 查看所有远程库
git remote -v 
# 添加上游git地址
git remote add upstream 上游git地址
git remote -v 
# 从上游仓库fetch分支和提交点，传送到本地，并会被存储在一个本地分支 upstream/master
git fetch upstream 
# 切换到主分支
git checkout master
# 把upstream/master合并到本地分支上，不会丢掉本地代码
git merge upstream/master 
# 推送代码到自己的分支
git push orign master 
# 更新代码
git pull orign master

#=======================第二次以上同步=================
git fetch upstream
git checkout master
git merge upstream/master
git push orgin master
git pull orgin master

```

