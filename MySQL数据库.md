# MySQL数据库

---

关系性数据库

ACID原则: 事务(transaction) 

- 原子性(atomicity): 全部成功才成功
- 一致性(consistency) : 此消彼长,约束一致
- 独立性(Isolation): 只要没提交,并发事务访问数据互不影响
- 持久性(durability):提交后永久保存

默认端口: 3306

> MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。
>
> 开发这个分支的原因之一是：甲骨文公司收购了MySQL后，有将MySQL闭源的潜在风险，因此社区采用分支的方式来避开这个风险。
>
> MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

## 配置环境

### 下载

```shell
wget 镜像地址
```

### 解压

```shell
tar xf 软件包.tar
```

### 安装

```shell
yum install -y *.rpm
# 默认安装位置/var/lib/mysql


# 1. 编辑创建mariadb.repo仓库文件
vi /etc/yum.repos.d/MariaDB.repo
# 一行命令轻松安装 MariaDB
yum install MariaDB-server MariaDB-client -y
# 2、添加repo仓库配置
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

```

### 启动

```shell
systemctl start mysql # 启动mysql 
systemctl start mariadb  #启动MariaDB
systemctl stop mariadb  #停止MariaDB
systemctl restart mariadb  #重启MariaDB
systemctl enable mariadb  #设置开机启动
```

### 报错

```shell
setenforce 0 # 切换 SElinux 宽容模式
```

### 配置文件

```shell
vim /etc/my.cnf

datadir= /xxx/mysql 数据库目录
socket=/xxx/mysql/mysql.sock
## 主服务器配置
server-id=1  设置id
log-bin=/xxx/log/master-bin 主
sync_binlog=1 确保主从复制事务安全 
## 从服务器配置
server-id=12  设置id 不同于主id
log-bin=/xxx/log/slave-log
sync_binlog=1 确保主从复制事务安全 
read_only=ON
## 添加默认参数
[mysql]
/xx/xx/mysql.sock

# 查询默认密码 ?FKFm8K7CW-j
grep 'pass' /var/log/mysqld.log

# 初始化(重置密码...)
mysql_secure_installation 
```

## mysql 命令

### 密码校验

```shell
## 设置密码校验规则
mysql> set global validate_password_policy =0/1/2;
# 0 密码校验级别最低, 只校验密码长度
# 1 必须包括大小写、数字、特殊符号
# 2 满足0、1追加 不能出现单词

## 修改密码最短长度
set global validate_password_length=3;
```

### 创建普通用户

```shell
# 生产环境不会使用root用户, 为了数据库安全需要新建其他用户分配权限
> create user 'username'@'ip' identified by 'password' # @'%' %代表所有
> use mysql;
> select host,user,password from user where user='username';
#检查主库创建的复制账号
select user,host from mysql.user;
#检查授权账号的权限
show grants for username@'%';
```

 ### 权限操作

```shell
## grant 权限 on 数据库.表名 to 账户@主机名            对特定数据库中的特定表授权
## grant 权限 on 数据库.* to 账户@主机名            　　对特定数据库中## 的所有表给与授权
## grant 权限1,权限2,权限3 on *.* to 账户@主机名   　　 对所有库中的所有表给与多个授权
## grant all privileges on *.* to 账户@主机名   　　 对所有库和所有表授权所有权限

## 查看权限:
mysql> show grants;
## 用户授权:
mysql> grant all on *.* to 'root'@'%' identified by 'password'; 
## 刷新权限:
mysql> flush privileges; 


## 授予远程登录的权限
MariaDB [(none)]> grant all privileges on *.* to username@"%" identified by 'password';
## 移除权限
MariaDB [(none)]> revoke all privileges on *.* from username@"%";

```

### 实例运用

```mysql
-- 创建数据库
create database vote default charset utf8;
-- 创建新用户/密码
create user 'hellokitty'@'%' identified by 'Hellokitty.618';
-- 新用户设置权限
grant all privileges on vote.* to 'hellokitty'@'%';
-- 刷新权限
flush privileges;

-- 切换数据库
use vote;

-- 创建学科表
create table `tb_subject`
(
	`no` integer auto_increment comment '学科编号',
    `name` varchar(50) not null comment '学科名称',
    `intro` varchar(1000) not null default '' comment '学科介绍',
    `is_hot` boolean not null default 0 comment '是不是热门学科',
    primary key (`no`)
);
-- 创建老师表
create table `tb_teacher`
(
    `no` integer auto_increment comment '老师编号',
    `name` varchar(20) not null comment '老师姓名',
    `sex` boolean not null default 1 comment '老师性别',
    `birth` date not null comment '出生日期',
    `intro` varchar(1000) not null default '' comment '老师介绍',
    `photo` varchar(255) not null default '' comment '老师照片',
    `gcount` integer not null default 0 comment '好评数',
    `bcount` integer not null default 0 comment '差评数',
    `sno` integer not null comment '所属学科',
    primary key (`no`),
    foreign key (`sno`) references `tb_subject` (`no`)
);
```

### Django中的mysql配置

- django默认mysql库: MySQLdb

- 替换: mysqlclient(首选)  / pymysql  (Django2.2及以上存在兼容性问题)

  ```python
  ## Django项目目录 __init__.py添加代码
  import pymysql
  pymysql.install_as_MySQLdb()
  
  ## 配置settings.py文件
  # 添加已安装应用
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'polls',
  ]
  # 配置mysql连接参数
  DATABASES = {
      'default': {
          # 数据库引擎配置
          'ENGINE': 'django.db.backends.mysql',
          # 数据库的名字
          'NAME': 'vote',
          # 数据库服务器的IP地址（本机可以写localhost或127.0.0.1）
          'HOST': 'localhost',
          # 启动MySQL服务的端口号
          'PORT': 3306,
          # 数据库用户名和口令
          'USER': 'hellokitty',
          'PASSWORD': 'Hellokitty.618',
          # 数据库使用的字符集
          'CHARSET': 'utf8',
          # 数据库时间日期的时区设定
          'TIME_ZONE': 'Asia/Chongqing',
      }
  }
  ```

  



###  启动客户端

```shell
$ mysql -uroot -ppassword -hhost
#查看当前登录用户
mysql> select user();
# 设置密码
mysql> set password=password('xxx')

```

### 常用命令

```mysql
mysql> use dbname; ## 选择数据库
mysql> set names utf8; ## 设置使用的字符集
CREATE DATABASE 
CREATE TABLE
DROP TABLE
CREATE INDEX 
DROP INDEX
```



### 增删改查

```mysql
 ## 查找 SELECT
 SELECT * FROM table;
 SELECT column1,column2 FROM table;
 # DISTINCT 显示不同值,去重
 SELECT DISTINCT column FROM table;
 # WHERE + 查询条件(比较操作符 BETWEEN  LIKE IN ) AND OR 
 # ORDER BY 排序默认升序 ASC | DESC
 # 
 ## 更新 UPDATE 缺少WHERE语句将更改全部
 UPDATE table SET column1=value1,column2=value2 WHERE condition 
 ## 插入 INSERT INTO
 INSERT INTO table VALUES (value1,value2...)
 INSERT INTO table(column1,column2..) VALUES (value1,value2...)
 ## 变更  ALTER TABLE
 ALTER TABLE
 ## 删除  DELETE
 DELETE
 
 
```







### mysql备份和恢复

```shell
## 备份数据到db.dump文件
$ mysqldump -u root -p --all-databases > /tmp/db.dump
## 数据恢复
$ mysql -uroot -p < /tmp/db.dump
```



### 主从服务器配置

![img](https://pythonav.com/media/uploads/2019/02/22/Gobook/Chapter7/pic/m2.png)

![img](https://pythonav.com/media/uploads/2019/02/22/Gobook/Chapter7/pic/m3.png)

#### master 主库

```shell
# 帮助文档:配置复制的参数，Slave从库连接Master主库的配置
mysql> help change master to;

# 新建用于主从同步的用户username,允许登录的从库是'%'
create user 'username'@'%' identified by 'password';
#题外话：如果提示密码太简单不复合策略加在前面加这句
mysql> set global validate_password_policy=0;
## 给从库账号授权,说明给username从库复制的权限，在"%"机器上复制
mysql> grant replication slave on *.* to 'slave'@"host"  identified by 'xxxx';

## 实现对主数据库锁表只读，防止数据写入，数据复制失败
mysql> flush table with read lock;
## 锁表后，一定要单独再打开一个SSH窗口，导出数据库的所有数据，
$ mysqldump -uroot -p --all-databases > /data/all.sql 
##导出数据完毕后，解锁主库，恢复可写；
mysql> unlock tables;

## 检查主库的状态
MariaDB [(none)]> show master status
## 将备份导出的数据scp至Slave数据库
$ scp /data/all.sql root@ip:/data/

#查看数据库状态
$ systemctl status mariadb

## 配置master主库
# 1. 修改配置文件
$ vim /etc/my.cnf
# 文件编辑
　　[mysqld]
　　server-id=1  # server-id服务的唯一标识（主从之间都必须不同）
　　log-bin=mysql-bin # log-bin启动二进制日志名称(自定义)为mysql-bin 
#2. 重启mariadb
$ systemctl restart mariadb


```

#### slave 从库

```shell
## 配置slave从库
# 设置server-id值并关闭binlog功能参数
# 1. 修改Slave的/etc/my.cnf
[mysqld]
server-id=3 # server-id唯一
# 2. 重启mariadb
$ systemctl restart mariadb
# 3. 检查Slava从数据库的各项参数
mysql> show variables like 'log_bin';
mysql> show variables like 'server_id';
## 恢复主库Master的数据导入到Slave库
# 导入数据（注意sql文件的路径）
mysql> source /data/all.sql;
# 7.启动从库的同步开关，测试主从复制的情况
start slave;
# 8.查看主从复制状态
show slave status\G;


## 注意此处还未配置从库的只读模式，只需在slave服务器上配置/etc/my.cnf，加上以下配置，并且在slave上创建普通用户，使用普通用户主从同步即可达到只读的效果

# 如果用root用户，无法达到readonly，这是一个坑
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
log-error=/var/log/mysqld.log
server-id=3
read-only=true
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```



### MySQL优化

