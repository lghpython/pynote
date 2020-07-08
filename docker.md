#### 如何保持环境一致

```shell
pip3 freeze > requirement.txt
pip3 install -r requirement.txt
  
```

## docker

- 一处编译到处运行
- 对系统的消耗不多
- 可以快速启动
- 维护简单,扩展容易
- 与传统虚拟机比较,有优势

### 镜 像 image

可以理解为操作系统的iso镜像, 相当于oop中的类 容器相当实例

### 容 器 container

运行后的镜像

### 仓 库 repository

- 集中存放镜像
- 私有仓库
- 公共仓库 (docker hub、docker pool)



### 底层实现原理

namespace

cgroup

linux内核技术![t2](https://pythonav.com/media/uploads/2019/02/22/Gobook/Chapter11/pic/t2.png)

### 安装

docker-ce 社区版本

docker-ee 商业版本

```shell
cd etc/yum.repos.d/
yum install -y 

## 配置加速器
vim /etc/docker/daemon.json
##
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
##
# 重启docker
systemctl daemon-reload
systemctl restart docker
```

### docker 命令

```shell
docker --help # 帮助
# 语法
docker [options] command [arg..]
#获取镜像 hello-world
docker pull hello-world
#检查镜像
docker images
docker image ls #
docker image -q # 显示镜像的id
#运行image文件,可以用容器id
docker run hello-world
#检查docker容器进程
docker ps
#检查所有运行过的容器
docker ps -a
# 搜索
docker search centos  #搜索所有centos的docker镜像
INDEX  NAME(名称)  DESCRIPTION(描述) STARS(下载次数)  OFFICIAL(官方)   AUTOMATED(自动化)
#获取centos镜像
docker pull centos  
#删除docker镜像
docker rmi imageID/imageName
#导入本地镜像到docker镜像库
docker load < /opt/centos.tar.gz   


```

