```shell
# 安装smb服务
sudo apt-get install samba samba-common smbclient -y
sudo apt-get --purge remove samba samba-common

# 创建共享目录并附权限
mkdir -p /data/smb/share
sudo chmod -R 777 /data/smb/share

# 备份配置
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak

# 修改配置，在/etc/samba/smb.conf最后一行加入
[share]
comment = my share directory
path = /data/smb/share
browsable = yes
writable = yes

# ===================解释=======================
comment = my share directory # 说明
path = /data/share # 共享路径
browsable = yes # 可浏览
writable = yes # 可写入
# =============================================

# 重启smb服务
sudo systemctl restart nmbd &  systemctl restart smbd

# ===============用户管理=====================
sudo smbpasswd -a 用户名 #加smb用户并设置访问密码(该账户必须存在于/etc/passwd文件中)
sudo smbpasswd -d 用户名 #禁用一个samba用户
sudo smbpasswd -e 用户名 #恢复一个samba用户
sudo smbpasswd -x 用户名 #删除一个samba用户
sudo pdbedit -L #查看samba服务器中已拥有哪些用户

# ==================访问=========================
smbclient //58.206.101.115/share 
```

