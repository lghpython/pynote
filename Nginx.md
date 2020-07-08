## Nginx 

目前能提供 Web 网络服务的程序有 IIS、Nginx 和 Apache 等

lvs 负载均衡 章文嵩博士

vue 全能框架 尤雨溪

Tengine 淘宝以Nginx基础发起的Web服务器项目

> web服务器（nginx）：接收HTTP请求并返回数据
>
> web框架（django，flask）：开发web应用程序，处理接收到的数据`

### 优点: 

- nginx是一个开源的，支持高性能，高并发的www服务和代理服务软件
- nginx比它大哥apache性能改进许多，nginx占用的系统资源更少，支持更高的并发连接，有更高的访问效率。
- 可以作为反向代理，负载均衡，以及缓存服务使用。
- 安装更为简单，方便，灵活。
- 支持高并发，能支持几万并发连接
- 资源消耗少，在3万并发连接下开启10个nginx线程消耗的内存不到200M
- 支持异步网络i/o事件模型epoll

### 安装

```shell
## 安装依赖包
yum install gcc patch libffi-devel python-devel  zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel openssl openssl-devel -y
# gcc编译依赖
yum install gcc-c++
# perl库,nginx 的 http 模块使用 pcre 来解析正则表达式
yum install -y pcre pcre-devel
#  nginx 使用 zlib 对 http 包的内容进行 gzip
yum install -y zlib zlib-devel
# nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。


## 编译安装
# 1.下载源码包
wget -c https://nginx.org/download/nginx-1.12.0.tar.gz
# 2.解压缩源码
tar -zxvf nginx-1.12.0.tar.gz
# 3.配置，编译安装  开启nginx状态监测功能
./configure --prefix=/opt/nginx112/ 
make && make install 
# 4.启动nginx，进入sbin目录,找到nginx启动命令
cd sbin
./nginx #启动
./nginx -s stop #关闭
./nginx -s reload #重新加载
# 5.修改PATH
PATH=$PATH:/opt/nginx112/

# 查看
ps -ef | grep nginx
# 查看端口
ss -tnlp | grep nginx
# 关闭防火墙
iptables -F
```

### 目录结构

```shell
[root@localhost nginx]# ls
conf  html  logs  sbin

# conf 存放nginx 所有配置文件的目录,主要nginx.conf
# html 存放nginx默认站点的目录(静态),如index.html、error.html
# logs 存放nginx默认日志的目录,如error.log 、access.log
# sbin 存放nginx主命令的目录(动态), sbin/nginx

```

### 命令格式

```shell
[root@localhost sbin]# ./nginx -h
nginx version: nginx/1.19.0
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /opt/nginx/)
  -c filename   : set configuration file (default: conf/nginx.conf)
  -g directives : set global directives out of configuration file

```



### Nginx主配置文件

- /opt/conf/nginx.conf

```shell
egrep -v '#|^$' /opt/nginx1-12/conf/nginx.conf
## 得到配置文件内容
worker_processes  1; # 工作进程的个数,配置成村上的核心数-1或-2
# cpu的亲缘性绑定,让nginx的子进程工作在每个cpu上

events {
    worker_connections  1024; # 每个worker进程支持的最大连接数
    # use epoll/select/poll; # 时间驱动模型,不写默认epoll
}
http {
    include       mime.types; # 导入
    default_type  application/octet-stream; # 默认请求方式
    sendfile        on;
    keepalive_timeout  65; # 保持长连接超时时间
    # 多域名访问,多个server{ ... } 多个虚拟主机
    server { 
    		# listen 80 default; # 默认server 
        listen       80; # 监听端口
        server_name  localhost; # 设置域名,host更改
        location / {
            root   html; # 指定静态文件地址
            index  index.html index.htm; # 指定默认页面
        }
        # 错误页面, 5xx服务端错误
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

- 启动以后生成主进程,根据配置文件选项来生成子进程(工作进程) ,主进程不负责处理用户请求,只转发用户请求给子进程处理

### 虚拟主机

- 基于ip地址

- 基于域名(最简单)

- 基于端口

- ```shell
  server { 
  	listen 80 default; # 默认server 
  	...
  }
  server { 
    listen 80 ; 
  	...
  }
      		
  ```

  

### nginx 日志

```shell

log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';
access_log  logs/access.log  main;
    
## remote_addr 访问客户端IP地址
## remote_user 访问的用户 没有“-”
## time_local 本地时间 [14/Aug/2018:18:46:52 +0800]
## request 请求方式,请求地址,请求协议“GET /地址 HTTP/1.1”
## status 状态吗
## body_bytes_sent 发送请求体大小
## http_referer 对应“-” 直接输入
## http_user_agent 客户端身份信息
## http_x_forwarded_for 记录客户端的来源真实ip地址

```

### 禁止访问

```shell
server{ 
	[deny xx.xx.xx.xx;
	allow x.xx.xxx.xx;]
	...
	location / {
		[deny xx.xx.xx.xx;
		allow x.xx.xxx.xx;]
		...
	}

```

### Nginx 反向代理

- nginx实现负载均衡的组件  (F5、 A10、 lvs 、haproxy 、nginx)
- 保护网站安全的作用
- 缓存静态文件

```shell
ngx_http_proxy_module   ##  proxy代理模块，用于把请求抛给服务器节点或者upstream服务器池
# 两台服务器
master  192.168.11.63　　主负载
slave   192.168.11.64　　web1

## 配置文件设置
server{
	listen 80;
  server_name  192.168.11.63;
  location / {
  	proxy_pass  http://192.168.11.64;
  }
}
				
```



### Nginx 均衡负载

- 集群
- upstream配置

```shell
####  upstream配置
upstream django {
       server 10.0.0.10:8000
       server 10.0.0.11:9000 
}
## 添加proxy_pass
location / {
            root   html;
            index  index.html index.htm;
            proxy_pass http://django;
}


### upstream分配策略
# 调度算法   　　 概述
# 轮询    　　　　按时间顺序逐一分配后端服务器(默认)
# weight  　　   加权轮询,weight值越大,分配到的访问几率越高
# ip_hash   　　 每个请求按访问IP的hash结果分配,这样来自同一IP的固定访问一个后端服务器
# url_hash   　  按照访问URL的hash结果来分配请求,是每个URL定向到同一个后端服务器
# least_conn    最少链接数,那个机器链接数少就分发
upstream django {
			 # ip_hash; # 同IP的固定访问一个后端服务器
       server 10.0.0.10:8000 weight=5;
       server 10.0.0.11:9000 weight=10;#这个节点访问比率是大于8000的
}

### backup 在非backup机器繁忙或者宕机时，请求backup机器，因此机器默认压力最小
upstream django {
       server 10.0.0.10:8000 weight=5;
       server 10.0.0.11:9000;
       server xx.xx.xx.xx backup; #表示备用服务器
}
```

#### 关闭防火墙

```shell
iptables -F
sed  -i 's/enforcing/disabled/' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld
```

#### 配置静态资源、图片

```shell
cat nginx.conf
server {
        listen       80;
        server_name  192.168.119.11;
        #定义网页根目录
         root /code;
        #定义了静态资源
        index index.html;
#域名匹配，所有的png、jpg、gif请求资源，都去/root/code/images底下找
         location ~* .*\.(png|jpg|gif)$ {
                root /code/images;
        }    
#重启nginx
./sbin/nginx

wget xx.jpg -O 1.jpg # -O 重命名
```

#### location配置

```shell
## =号精确匹配
location = / { 

    [ configuration A ]
}
## /开头匹配
location / { 
    [ configuration B ]
}
## 匹配/documents/
location /documents/ { 
    [ configuration C ]
}
## 匹配/images/开头
location ^~ /images/ { 
    [ configuration D ]
}
## 配置~*不区分大小写 以.(gif|jpg|jpeg)结尾
location ~* \.(gif|jpg|jpeg)$ {
    [ configuration E ]
}
## 优先级: = 完整路径 ^|~ / 
# /status 查看当前负载情况
location /status{
		stub_status on;
}
```

### 压缩

```shell
gzip on # 开启压缩
# 提高响应速度, 节省带宽
```

----

### Django部署

### wsgi

- Django自带的wsgiref 在调试模式下使用的wsgi,请求能力有限,需要安装usgi
- WSGI: Web Server Gateway Interface 描述web服务器(如nginx)如何与web应用程序(如django)通信的规范
- uwsgi: 是uWSGI服务器的独占通信协议,用于定义传输信息的类型
- uWSGI: 全功能的app服务器,实现了WSGI协议、uwsgi协议、http协议等.把http协议转换为语言支持的网络协议
- UWSGI: 具体实现方式

#### 安装

```shell
## python3 yum安装 
yum install -y python python-pip python-devel

## 安装django
pip3 install django==1.11 -i https://pypi.douban.com/simple 
# Django创建项目复习
django-admin startproject mysite
# 启动服务器
python3 managy.py runserver xx.xx.xx.xx:xx
# centos7无法访问,设置允许哪些host访问
vim mysite/setting.py  
DEBUG=False
ALLOWED_HOSTS=['*']  # 设置*允许所有host访问


## Django自带的wsgiref 在调试模式下使用的wsgi,请求能力有限 , python 安装uwsgi
pip3 install uwsgi -i https://pypi.douban.com/simple 
# 查找帮助
uwsgi --help | grep 'http'
## 切换Django目录,后启动
cd  /xx/mysite/
uwsgi --http :8080 --module mysite.wsgi 
## 以配置文件启动
uwsgi --ini /etc/uwsgi.ini
## 报错查询日志
tail -f mysite/
```

#### #### uwsgi配置文件格式

```shell
## conf、py、cnf、xml、json、ini、yaml
## 配置文件/etc/下添加 uwsgi.ini
[uwsgi]
# http= :8080 # 1⃣️ http
socket=:9009  # 2⃣️ socket 推荐
# socket=file.sock # 3⃣️ socket文件
# 项目路径
chdir=/xx/mysite
#uwsfi的文件
wsig-file= mysite/wsfi.py
#虚拟环境
# virtualenv = /xxx/venv
# 进程个数
processes=4
# 线程个数
threads=2
# 后台启动,指定日志的输出
daemnize=/xx/mysite/django.log
# 清除临时文件
vacuum=true
# python 文件发生改变自动重启
py-autoreload=1
```

- uwsgi无法处理静态文件,使用nginx配置

  ```shell
  ## nginx.conf配置 
  server{
  	listen 80;
  	server_name xx.xx.com;
  	# 访问位置当监听到80端口访问http://127.0.0.1:8080;
  	location / {
  	# 3⃣️ 对应uwsgi配置文件的socket配置
  	include uwsgi_params;
  	uwsgi_pass unix://file.sock;
  	# 2⃣️ 对应uwsgi配置文件的socket配置
  	include uwsgi_params;
  	uwsgi_pass 127.0.0.1:9009;
  	# 1⃣️对应uwsgi配置文件的http配置
  	# proxy_pass http://127.0.0.1:8080;
  
  	}
  	# 访问位置/data/suprecrm/statics/
  	location /statics {
  	root /data/suprecrm; 
  	}
  }
  
  # django中setting.py 设置
  STATIC_ROOT=os.path.join(BASE_DIR,'static/')
  # 收集静态文件
  python3 manage.py collectstatic
  ```

![屏幕快照 2020-06-04 下午9.47.04](/Users/lgh/Desktop/屏幕快照 2020-06-04 下午9.47.04.png)

### 三种配置方式

```shell
## 配置文件/etc/下添加 uwsgi.ini
[uwsgi]
# http= :8080 # 1⃣️ http
socket=:9009  # 2⃣️ socket 推荐
socket=file.sock # 3⃣️ socket文件

## nginx.conf配置 
location / {
	## 3⃣️ 对应uwsgi配置文件的socket配置
	include uwsgi_params;
	uwsgi_pass unix://file.sock;
	## 2⃣️ 对应uwsgi配置文件的socket配置
	# include uwsgi_params;
	# uwsgi_pass 127.0.0.1:9009;
	## 1⃣️对应uwsgi配置文件的http配置
	# proxy_pass http://127.0.0.1:8080; #通用方式
	}
	
# 2⃣️为官方推荐使用uwsgi_pass 比1⃣️块10倍
# 本地使用 unix文件的方式
# 非本地 使用端口的方式
```

