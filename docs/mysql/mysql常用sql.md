推荐 [C语言中文网-mysql教程](http://c.biancheng.net/view/2373.html )

#### 连接

```shell
#本地连接： 
mysql -u用户名 -p密码;
#远程连接： 
mysql -h 192.168.5.116 -P 3306 -u root -p123456
```

#### 修改密码

```mysql
set password for root@localhost=password("新密码"); 
-- 
之后要刷新权限 
flush privileges;
```

#### 创建用户

- sql:

  ```mysql
  CREATE USER 'username'@'host' IDENTIFIED BY 'password';
  ```

- 说明：
  
  - %允许来自任何ip的连接，localhost允许本机的连接， 'dog'@'localhost'和 'dog'@'%'是完全不同的两个账号
  - username：你将创建的用户名
  - host：指定该用户在哪个主机上可以登陆，如果是本地用户可用localhost，如果想让该用户可以从任意远程主机登陆，可以使用通配符%
  - password：该用户的登陆密码，密码可以为空，如果为空则该用户可以不需要密码登陆服务器
  
- demo

  ```mysql
    CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';
    CREATE USER 'pig'@'192.168.1.101_' IDENDIFIED BY '123456';
    CREATE USER 'pig'@'%' IDENTIFIED BY '123456';
    CREATE USER 'pig'@'%' IDENTIFIED BY '';
    CREATE USER 'pig'@'%';
  ```

#### 授权:

- sql

```mysql
GRANT privileges ON databasename.tablename TO 'username'@'host'
```

- 说明

  - privileges：用户的操作权限，如SELECT，INSERT，UPDATE等，如果要授予所的权限则使用ALL

  - databasename：数据库名

  - tablename：表名，如果要授予该用户对所有数据库和表的相应操作权限则可用*表示，如*.*

- demo

  ```mysql
  GRANT SELECT, INSERT ON test.user TO 'pig'@'%';
  GRANT ALL ON *.* TO 'pig'@'%';
  GRANT ALL ON maindataplus.* TO 'pig'@'%';
  注意:
  用以上命令授权的用户不能给其它用户授权，如果想让该用户可以授权，用以下命令:
  GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
  ```

  

- 查看授权


```mysql
SHOW GRANTS FOR user@localhost;
```

#### 撤销授权

```mysql
REVOKE ALL ON *.* FROM user@%;
```



#### 常用sql

```mysql
show databases;
select version();
select uuid();
select replace(uuid(),"-","") as uuid;
select sysDate();
select database(); -- 查看当前数据库
```



#### 创建表

```mysql
CREATE TABLE `student` (
	`id` VARCHAR(50) NOT NULL,
	`name` VARCHAR(50) NULL DEFAULT NULL,
	`grade` VARCHAR(50) NULL DEFAULT NULL,
	`score` INT(11) NULL DEFAULT NULL,
	PRIMARY KEY (`id`)
)
DEFAULT CHARSET=utf8
COLLATE='utf8_general_ci'
ENGINE=InnoDB;

-- 插入数据
insert into student (id,name,grade,score) values (uuid(),'张三','一年级',80);
insert into student (id,name,grade,score) values (uuid(),'张四','一年级',85);
insert into student (id,name,grade,score) values (uuid(),'张五','一年级',91);

insert into student (id,name,grade,score) values (uuid(),'李四','二年级',75);
insert into student (id,name,grade,score) values (uuid(),'李五','二年级',71);
insert into student (id,name,grade,score) values (uuid(),'李六','二年级',99);

insert into student (id,name,grade,score) values (uuid(),'王五','三年级',95);
insert into student (id,name,grade,score) values (uuid(),'王六','三年级',56);
insert into student (id,name,grade,score) values (uuid(),'王七','三年级',55);

-- 查询每个年级最高分（group by 只能叉函数和分组字段，sql_mode = only_full_group_by,严格遵守ANSI SQL规则）
select max(score)  from student group by grade;
select * from student where score in (select max(score)  from student group by grade) order by score desc;
```



