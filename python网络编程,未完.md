### IP地址和MAC地址:

ARP协议: 地址解析协议，即ARP（Address Resolution Protocol），是根据IP地址获取物理地址的一个TCP/IP协议

ISO 7层模型: 应、表、会、传、网、数、物

ISO 5层协议: 应、传、网、数、物

第一层:物理层   

第二层:数据链路层  (物理MAC地址、ARP协议)

第三层: 网络层.      (ipv4、ipv6协议)

第四层: 传输层   (端口 port)

第五层:应用层 

### 套接字socket

基于网络类型的套接字家族: AF_INET

基于文件类型的套接字家族: AD_UNIX

### TCP协议和UDP协议







### 网络类型

---

网络的基础知识

ipv4 : 4段 32位

网络位+主机位

网段:

主机: 可以网络访问外网的设备

A : 网络位是8位 主机位24位 二进制开头0不可变

​	127开头 特殊用途,回环地址

​	可用网段2^7 可用主机 2^24-2

​	主机位全为0,表示一个网段

​	主机位全为1,表示网段里面的广播地址

​	私有地址:10.xx.xx.xx

​	子网掩码: 255.0.0.0

​	公有地址:

​	ip地址: 1.1.1.1/8

​	网段: 1.0.0.0

B: 网络位是16位 主机位16位 二进制开头为10不可变

​	第一段:128--191

​	可用网段: 2^14  可用主机2^16-2

​	私有地址: 172.16--172.31

​	子网掩码: 255.255.0.0

C: 网络位是24位 主机位8位 二进制开头为110不可变

​	192 --223

​	可用网段: 2^21 可用主机2^8-2

​	子网掩码:255.255.255.0

D: 网络位是32位  二进制开头为1110不可变

​	224--239

​	子网掩码: 255.255.255.255

E: 科研使用

只有ABC类地址可以用户使用

CIDR(无类域间路由)

​	网络位向主机位借位

ip地址和子网掩码确定网段

- 按位与 &
- 按位或 |
- 异或 ^
- 取反 ~    -(n+1) 
- 左移 << 乘2的次方
- 右移 >> 除2的次方(向下取整)

查询ip命令: ip a   (ifconfig快被取代) 

#### 网络配置

---

- 手动

  - ip
  - 配置文件

- 自动分配

  - dhcp

  

#### ip命令: ip a/addr

- 添加ip到网卡上: ip a add 192.168.x.x dev ens33 
- 删除ip地址: ip a del 192.168.x.x dev ens33 
- 新增ip取别名: ip a add 192.168.x.x/24 dev label ens33:0

#### 网卡配置文件

- 配置文件: /etc/sysconfig/network-scripts/ifcfg-name

  ```shell
  [root@localhost /]# cat /etc/sysconfig/network-scripts/ifcfg-ens33
  TYPE=Ethernet #网卡接口类型
  PROXY_METHOD=none
  BROWSER_ONLY=no
  BOOTPROTO=dhcp  # 获取ip地址方式可与是dhcp, static,none
  DEFROUTE=yes
  IPV4_FAILURE_FATAL=no
  IPV6INIT=yes
  IPV6_AUTOCONF=yes
  IPV6_DEFROUTE=yes
  IPV6_FAILURE_FATAL=no
  IPV6_ADDR_GEN_MODE=stable-privacy
  NAME=ens33  #网卡名
  UUID=08861baa-8833-4a1b-b8a5-d947915bc0a9 # 设备唯一标识码
  DEVICE=ens33  # 设备文件
  ONBOOT=yes  # 开机自启
  IPV6_PRIVACY=no
  HWADDR="" # mac地址
  IPIDDR=x.x.x.x # 设置IP地址
  NETMASK=255.255.255.255 #设置子网掩码
  GATEWAY=192.x.x.x #网关 和外界互联
  ```

- 重启网络 systemctl restart network

- DNS配置: /etc/resolv.conf

  ```shell
  namesever 192.168.x.x
  nameserver 8.8.8.8  # 谷歌DNS
  nameserver 114.114.114.114 # 电信DNS
  nameserver 202.106.114.1 # 联通DNS
  
  ```

  

#### 主机名

- hostname : 获取主机名
- hostname name 临时设置主机名
- 永久配置,写入文件:
  - hostnamectl set-hostname name
  - 写入配置文件
    - centos6: /etc/sysconfig/network
    - centos7:/etc/hostname

#### ss、netstat(cento6) 打印网络状态信息

- -a 所有
- -l 监听中
- -t tcp
- -u udp
- -x unix socket文件
- -n 显示端口号
- 常用组合: -anlp 、-tnlp、-unlp
- *表示 ipv4 0.0.0.0 表示当前主机上所有IP地址
- :: 表示 ipv6

#### 解析(域名-->ip地址)

- 本地解析 /etc/hosts 优先
- dns解析
- 根节点 总共13组

#### wget: 下载网络资源

```shell
-q 静默模式
-c 断点续传
-o filename 重命名
-P dirname 保存指定目录
-r 递归下载
-p 下载所有的html文件
```

#### systemctl 管理服务  service (centos6)

```shell
start name 启动服务
restart name 重启服务 
stop name
reload name重读配置文件 平滑重启
status name 查看状态
enable name 开机自启
disable name 关闭开机自启
list-unit-files | grep name 查看开机启动状态

centos6
service 动作服务
chkconfig 管理开机服务
chkconfig name on 
```

#### 计划任务/定时任务

- 

