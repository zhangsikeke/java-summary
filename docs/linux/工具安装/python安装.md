#### 安装python

```shell
sudo apt-get install python3
```

#### 替换已有的python

```shell
# 下载解压到当前目录
wget https://www.python.org/ftp/python/3.6.9/Python-3.6.9.tgz
tar xfz Python-3.6.1.tgz
# 编译安装
cd Python-3.6.9
sudo ./configure --with-ssl
sudo -j4 make  # make -j4 使用4个编译命令并行编译，提高编译效率
sudo make install

# 备份旧python
cd /usr/bin
sudo mv python python.backup
sudo mv pip pip.backup

# 创建软连接
sudo ln -s /usr/local/bin/python3.6 /usr/bin/python
sudo ln -s /usr/local/bin/pip3.6 /usr/bin/pip

# 只为当前用户安装某个软件
https://blog.csdn.net/bdss58/article/details/72962896
```

