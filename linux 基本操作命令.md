## 

linux ---centos7

### 终端:

-  设备终端
- 物理终端
- 虚拟终端   ctrl+alt+f[1-6] /dev/tty#
- 图形终端/dev/tty7
- 串行终端
- 伪终端 通过ssh远程连接的 /dev/pts/#
- 查看终端命令 tty
- 查看春地址命令 ip a 或ip addr

### 用户

- 添加用户 useradd username

  ```shell
  useradd [options] LOGIN
  useradd -D 
  用户id递增,系统id创建
  -d 指定用户的家目录
  -g 指定用户组的id
  -G 指定用户附加组
  -k 指定复制那个文件下的内容和-m 一起使用 
  -m 创建用户家目录
  -c “message”指定用户描述信息
  -N 不创建同名的组,以user为组
  -s 指定用户登陆后的使用的shell
  -u 指定用户的id
  -D 显示系统的默认配置
  -D [options] 可以修改系统的默认配置
  /etc/default/useradd创建目录的默认文件
  /etc/skel/*默认复制的文件
  ```

-  用户修改usermode

   ```shell
   -c 修改描述信息
   -d 修改家目录,默认不新建,-m 移动新目录
   -g 修改用户组
   -G 修改用户附加组
   -a 追加附加组
   -l newname修改的登陆名
   -L 锁定用户不能登陆系统,修改密码默认解锁
   -U 解锁用户
   -s 修改用还登入会的shell
   -u 修改用户的uid
   -e 年-月-日 修改用户过期时间
   ```

- 删除用户 userdel username 

  ```shell
  默认删除用户不删除用户家目录
  -r 删除家目录
  -f 强制删除
  默认登陆状态下不能删除用户 强制删除仍可用
  ```

-  查看用户id 

   ```she
   -g 只显示组id
   -G 只显示附加组id
   -u 只显示用户id
   -n 显示名称,需要和上面选项配合	
   ```

-  切换用户

   ```shell
   su [options] [-] [USER [arg] ...]
   完整切换(切换变量环境) : su - username
   不完整切换(不切换变量环境) su username
   root用还要密码
   非root用户切换:需要密码
   su - xxx -c “whoami”
   ```

-  普通用户执行本身不能执行的命令更改sudo 配置

   ```shell
   /etc/sudoers 设置普通用户有root命令权限
   username ALL=(ALL)  PASSWD:ALL
   ```

- /etc/passwd文件

  - 用户名称:
  - 密码使用x来占位
  - uid
  - gid
  - 描述信息
  - 家目录
  - 登录后使用的shell

- 密码设置passwd

  ```shell
  passwd [options] <accountname>
  -d 删除密码,删除密码后不能登陆
  -e 再次登陆强制
  -l 锁定用户
  -u 解锁用户
  -f 强制操作
  -x maxday密码的最长时间
  ```

  

#### 交互式接口

- 启动终端以后,在终端设备上附加的一个应用程序
  - GUI(桌面)
  - CLI commandline
    - powershell
    - sh
    - bash (linux、mac默认的程序)
    - zsh
    - csh
    - tcsh

#### bash: 提供用户和操作系统之间的交互,接收用户的输入,发送给操作系统执行

- centos
- echo $SHELL
- cat /etc/shells : 查看当前系统可以使用的shell
- chsh -s /bin/shell :切换shell
- ctrl+d 快速终止当前连接
- 修改ssh连接满的步骤
  - grep -i 'dns' /etc/ssh/sshd_config
  - echo "UseDNS no" >> .etc.ssh/sshd_config 追加
  - systemctl restart sshd重启

#### 显示提示符格式:

echo PS1="\[\e[0;47;35m\][\u@\h \W]\\$\[\e[9m\]"'  >> /exc/profile.d/ps.sh 



### 常用命令:

- ls:列出文件、文件夹 ll
- cd 改变目录
- touch 新建文件
- cat 查看内通
- echo 回显、
  -  \>\>   写入文件
  - echo -e '\a' 播放声音
- mkdir 创建目录
  - -p 创建多级目录/递归创建目录
  - -v 显示详细过程
- rmdir 只删除非空目录
  - -p 删除多级空目录/递归删除空目录
- rm 删除
  - -r 递归删除
  - -f 强制删除
  - i 提示删除
  - -v 删除过程
  - -d 删除目录
  - rm -rf * 删除当前目录下所有
- mv 移动、重命名
  - -i 提示
  - -f 强制覆盖
  - -b 备份
  - -backup=number 备份后面夹数字
  - -v 显示过程
- cp  复制 本身命令时别名
  - -i 显示提示
  - -n 不覆盖
  - -r 或 -R 递归复制
  - -d 只复制链接文件,不复制原文件
  - -v 显示过程
  - -b 备份原来的文件
  - --backup=number 复制备份文件加上数字
  - -p 保留原来属性
- chmod: 更改读写权限
- chown: 更改用户权限
- alias 别名  xx = “xxx”  unalias取消别名
- source
- man
- help    获取帮助: 软件 -h
- history [number]
- whoami 显示当前登入用户。who am i 显示详细
- w 显示所有用户和详细信息
- date : 当前时间
  - date +%F
  - date +%M
  - date +%Y
  - date +%D
- timedatectl   时区(改时间: timedatectl set-timezone xxx/xxx
- cal 日历 cal -y 今年日历 cal 2018 指定年日历
- shutdown:一分钟后关机。
  - shutdown -c 取消 
  - shutdown -r 重启
  - shutdown +2 指定分钟后关机
  - shutdown 18:00 指定关机时间
  - shutdown now 立刻关机
- poweroff  // halt  //  init 0 关机
- reboot 重启 -f 强制重启 -p关机

### 文件目录结构:

- 严格区分大小写
- /根目录
- 隐藏文件: 以‘.’开头
- 路径分隔/

#### 文件命名规范

- 文件字符最长255个字符
- 包括路径在内的文件名称最长4095个字符
- 颜色表示
  - 蓝色 : 文件夹
  - 绿色 : 可执行文件
  - 红色: 压缩文件
  - 蓝绿色:链接文件 (快捷方式)
  - 白色:文件
- 除了斜杆和NULL,所有字符都可以使用 touch '@$^@#@'
- 大小写敏感

### 文件系统结构

- /boot 引导文件,内核文件
- /bin 所有用和都可以使用的命令
- /sbin 命令类管理
- /lib 启动程序时 使用的基本库文件 .so结尾
- /lib64 专门存放X86_64系统的辅助库文件
- /etc 配置文件存放
- /home/username 普通用户家目录
- /root 管理员的家目录
- /media 便携移动设备挂载点
- /mnt 临时文件挂载点
- /dev 设备文件和特殊文件存放
- /opt 第三方应用安装目录
- /tmp 临时文件存放
- /usr 安装程序
- /var 日志文件存放
- /proc 虚拟文件存放 内核启动和进程相关文件
- /sys 输出当前系统上的硬件相关文件
- /srv 系统上允许的服务用到的数据

### linux应用程序的组成

- 二进制文件
  - /bin
  - /sbin
  - /usr/bin
  - /usr/sbin
  - /usr/local/bin
  - usr/local/sbin
- 库文件
  - /lib
  - /lib64
  - /usr/lib
  - /usr/lib64
  - /usr/local/lib
  - usr/local/lib64
- 配置文件
  - /etc 
  - /etc/name
  - local/etc
- 帮助文件
  - /usr/share/man
  - /usr/share/doc
  - /usr/local/share/man
  - /usr/local/share/doc

绝对路径和相对路径

- 绝对路径
  - 以根/开始的 , 完整的文件存放位置
  - 可以读取到任何一个文件或文件夹
- 相对路径
  - 相对于当前位置决定 
  - 可以简短表示一个文件或文件夹
  - 父目录: cd ..

#### 目录名和基名

- basename:当前文件名 
- dirname:父级目录

#### 切换目录

- cd切换目录: change directory
  - 使用相对路径
  - 使用绝对路径
  - cd / 返回根目录
  - cd .. 返回父目录
  - cd -  返回上一次目录
  - cd ~ 返回家目录

- pwd 显示当前工作目录
  - pwd -p 链接的真正目录
- 列出目录和文件 ls
  - -a 所有文件
  - -l  显示详细信息/长格式显示
  - -R 多级列表/递归显示
  - -d 显示目录本身
  - -1分行显示
  - -S文件大小排序
  - -r倒序显示
  - -t时间顺序
  - -1h 易读内存
- stat 查看状态
  - Access 时间: 访问
  - Modify时间: 内容修改
  - Change时间: 权限修改
- touch 新建文件,已存在刷新 stat
  - -a 只修改atime和ctime
  - -m只修改mtime和学车么

- 命令展开: 
  - touch file{1..30..2} 
  - seq 2 20 4

#### 文件通配符

- *所有
- ? 匹配的任意单字符
- ~ 用户家目录
- [123]其中一个
- [0-9] 数字
- ls file[a-z] 字母(ASCII码顺序)
- [:lower:] 小写字母
- [:upper:]大写字母
- [:alpha:] 字母
- [:alnum:] 字母和数字
- [:digit:] 数字

### 目录树

- 安装: yum install -y tree
- tree 显示目录层级结构/目录树
- tree -d 只显示文件夹
- tree -dL Number 只显示Number层

### 复制

- 源文件时单个文件:
  - 目标不存在,新建并写入
  - 目标是文件,
  - 目标是文件夹,
- 源文件是多个文件, 目标必须是文件夹,否则报错
- 源文件是文件夹, 目标必须是文件夹,需要 -r
  - 目标文件夹存在目标文件就报错,不存在新建
  - 目标文件夹不存在创建并新建文件

### 软链接: 快捷方式

- 创建: ln -s 源文件 快捷方式
- 文件和目录可建快捷方式
- 指向另一个文件和目录的路径,大小是路径的符号长度
- 对磁盘引用次数没影响
- 可以跨分区
- 源文件发生变化,软连接会发生变化
- 源文件删除,软连接失效

### 硬链接: 磁盘引用次数发生变化

- 指向的是磁盘上同一块区域
- 磁盘引用次数会随链接次数增加
- 不能对目录做链接
- 不能跨分区
- 源文件发生变化,硬连接会发生变化
- 源文件删除后,硬连接不影响

### 查看文件类型

- file命令 查询文件 内容格式



### 输入和输出

- 标准输入. 默认键盘输入 stdin 0
- 标准输出 默认输出到终端窗口 stdout 1
- 错误输出 默认输出到终端窗口 stderr 2

### I/O重定向

- \> 覆盖
  - \> 标准输出重定向到文件中
  -  2> 错误输出重定向到文件中
  -  &> 所有输出重定向到文件中
- 禁止、允许覆盖
  - 禁止覆盖 set -C
  - 允许覆盖 set +C
- \>>追加 
  - \>>将标准输出追加到文件中
  - 2>> 将错误输出追加到文件中
  -  &>> 所有输出追加到文件中
- 标准输出和错误输入分开保存
  - ls f jdklfjd  \>  文件一   2>  文件二 
- 合并所有输出
  - &> 覆盖重定向
  - &>> 追加重定向
  - command > file 2>&1  覆盖下错误输出和标准
  - command >> file 2>&1 追加错误输出和标准输出
  - (command1:command2) 2>a 合并多个文件的输出 
  - (ls f; ls erhfjkd) &> /dev/null  回收站
  - /dev/zero 黑洞
    - 测试磁盘速度: dd if=/dev/zero of=j bs=100M count=2

### 输入

-  字符替换:tr
  - tr ‘a-z' 'A-Z'  < 文件名  小写替换大写
  - tr abc 12  替换a-->1 b-->2 c-->2 替换字符最后一位补足被替换字符
  - -d  删除  tr -d abc的字符
  - -t 截断 tr -t a
  - -s 去重  
  - -c  取反 
    - tr -sc a 除了a 其他去重
    - tr -dc a 删除a以外的,包括回车,无法显示结果 ctrl+d
  - tr -d a < f1 > f2 将f1删除a结果写入f2, 输入输出文件不能是同一个文件
- 多行多行发送给stdout
  - cat > f1 写入多行到f1
  - cat > f2 <<EOF  写入以EOF为出口,
    - EOF 不是固定的可自定义

### 管道 '|'

> 命令1| 命令2|命令3

- 命令1的结果作为2的输入得到输出结果作为3的输入得出最终结果
- 默认情况下管道只能传送标准输出
- 把错误也输出,需要在管道符后添加: 命令1|&命令2
- 有一些不接受管道的命令: 如touch 

### 文本相关命令

- cat	
  - -E 现实结尾$
  - -n 加行号
  - -b 非空行编号
  - -s 对空行压缩
- tsc (按行倒序现实) 与cat相反
- less 分屏现实文本(man命令默认分页)
  - 空格:下翻一屏
  - 回车: 下翻一行
  - n键 下一页
  - q键  退出
- more 显示分屏读取百分比
  - 显示完成自动退出
  - q键 退出
  - 空格: 下一行
  - -d 提示翻屏和退出命令
- head 默认显示前十行
  - 指定行数 :head -n 2 文件
  - 指定行数: head -2 文件
  - 指定显示多少字节: head -c 2 文件
- tail 默认显示后十行
  - 指定行数: tail -2 文件
  - 指定行数: tail -2 文件
  - 指定显示多少字节: tail -c 2 文件
- cut 切割
  - -c 按字符切割
  - -d  指定切割符 默认tab键 
  - -f# 显示第#个字段
    - cut -d: -f1 切分显示美行第一个
    - cut -d: -f2-5,7 切割显示2-5和7
- paste合并
  - 默认同行合并: paste a.txt b.txt
  - -d 指定间隔符.默认
  - -s 所有的行显示为列
- wc 文本统计(行数, 单词数, 字节数)
  - -l 只显示行数
  - -w 只显示单词数
  - -c 只显示字节数
  - -m 只显示字符数
  - -L 最长的一行长度
- sort 排序(默认字符排序)
  - -r 倒序
  - -R 随机排序
  - -n 按照数字排序
  - -f 忽略大小写
  - -t 指定位分隔符
  - -K# 指定到第#位
- uniq 合并相同的行
  - 相邻
  - 完全一样
  - -c 显示相同的行出现的次数
  - -d 只显示重复行
  - -u 显示没有重复的行
  - 组合使用 cut -d" " -f4|sort | uniq -c 指定" "分隔 排序 合并同类并奇数显示
- pv page
- uv user
- diff 对比不同文件 diff f1 f2 

### 权限操作

- chown:修改所有者和组

  - chown lgh file 修改所有者
  - chown :lgh file 修改所有组
  - chown root.root file
  - chown lgh:lgh file  修改所有者和组
  - chown --reference=c file
  - chown -R lgh file递归修改

- chgrp: 修改组

- chmod:修改读写权限 9位三组权限(所有者owner,所有组group,其他other)

  - 读权限: r
  - 写权限: w
  - 可执行权限: x
  - chmod u+x file 添加指定权限
  - chmod u-x file 删除指定缺陷
  - chmod u=x file 指定选权限
  - chmod a+x file 三组都添加x权限
  - 对于目录来说
    - r 读: 可以使用ls等文件查询命令
    - w写: 可以创建文件和删除
    - x可执行:可cd进入
  - 数字表示权限
    - --x  相当于 001 1
    - r-- 相当于 100  4
    - rw- 相当于 110  6
    - -wx 相当于 011 3 
    - r-x  相当于  101  5
    - rwx 相当于  111  7
  - 基于某个文件来更改

  #### 设定特殊权限

  - chattr +i file 添加i  不能更改、不能删除、不能变更
  - chattr -i file 删除i
  - chattr -a file 只能追加数据

#### 包的来源

- 光盘
- 第三方网站
- 官方网站
- 自己做
- https://pkgs.org
- epel第三方包的结合地

#### rpm包的查询

```shell
rpm -q 查询指定的包是否安装
-a  all 表示当前系统所安装的所有的包
-f filename 查询指定的文件由那个包安装生成
-c 查看指定的包生成了那些配置文件
-d 查询指定的包生成了哪些文档
-i 查询指定包的详细信息
-l 查询指定的包生成到文件
常用的选项: -qi -qa -ql -qc -qd
```

### yum

- yum会自动依赖关系
- 仓库存放的式多个包和包的元数据信息(所在仓库,依赖关系)
- 仓库位置:
  - http://
  - https://
  - ftp://
  - file://
  - 配置文件存放
  - 配置文件存放 /etc/yum.repos.d

```
备份源
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
下载源
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```

#### yum配置文件

- 查看配置文件 cat /etc/yum.repos.d/

```shell
#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib - mirrors.aliyun.com
failovermethod=priority # 前后优先级排序 roundrobin
baseurl=http://mirrors.aliyun.com/centos/$releasever/contrib/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/contrib/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/contrib/$basearch/
gpgcheck=1 # 是否校验包
enabled=0 # 是否启用 0 不启用
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7 # 校验文件

$releasever 发行版
$basearch 系统架构
```



#### yum 命令

- yum repolist	查看仓库
- yum list 列出仓库所有安装包
- yum install
  - -y 安装过程选项自动yes
- yum reinstall 重装
- yum update 更新所有
- yum update pkg 安装指定包
- yum downgrade pkg 降级需要安装包
- yum check-update 检查是否可以更新
- yum remove pkg 卸载指定包
- yum info pkg 显示包的详细信息
- yum clean 删除元数据信息/var/cache/yum
- yum makecache 重新构建
- yum search 搜索软件包 名称说明包含文本
- yum provides pkg 搜索命令由那个包提供的

#### 软件包组

- yum grouplist 列出包组
- yum groupinstall 安装包组
- yum groupinfo  过去包的信息
- yum groupupdate 更新包组
- yum groupremove 卸载包组

#### yum 选项命令

- -y 自动确认
- -q 静默模式

#### 源码安装

- wget pkgurl 下载(python为例)
- tar xzf pkg 解压
- cd pkgdir 切换目录
- 查看帮助信息: cat README / INSTALL
- 安装的所需文件
- 编译(检查程序所需文件)创建makefile 文件
- make 构建程序
- make install 安装到指定目录
- 配置环境变量 PATH

#### 磁盘管理

- df 查看磁盘空间占用

  ```shell
  -h 显示易读方式
  /dev/sd[a-z]  linux下磁盘命令格式
  ```

- du 查看目录的占用空间 du

  ```shell
  -s 查看目录
  -h 易读显示
  du -sh /*
  ```

- dd  测试读写速度

  ```shell
  dd if=/dev/zero of=file bs-size count=number
  if = input file 表示从输入文件读取
  of = out file 表示将独处的数据写入到什么地方
  bs = byte size 可以是K,M,G,T
  ```

- RAID 磁盘阵列卡

  ```shell
  rad0 读写数独提升 可用空间N(表示磁盘个数)*每个空间,没有容错能力,最小两块
  rad1 读提升,写下降 可用空间为所有中最小的大小,有容错能力,最小2块 2^N
  rad5 读写速度有提升,可用空间(N-1)*大小 有容错能力,最多可以坏1块硬盘,最小3块磁盘
  raid6 读写速度有提升,可用空间(N-2)*大小 有容错能力,最多可以坏2块,最少要4块
  raid10 先做raid1再做raid0 读写性能有提升,可用空间一半N*大小/2 有容错能力 最少4 ,每组镜像可以坏一组 
  raid01 先做raid0 再做raid1 读写性能有所提升可用空间为一半 有容错能力 最少4块 可以坏一个组
  
  ```

  ### 其他命令:

  #### find 查找:

  ``` shell
  find [options]..[查找路径][查找条件][处理动作]
  查找路径: 查找位置,默认当前文件夹
  查找条件: 指定查找的标准(文件名,大小、类型.)
  处理动作: 操作,默认输出屏幕
  ```

  ##### 查找条件:

  - 文件名查找
    - -name  文件名: find -name f1(可用正则)
    - -iname 忽略名称大小写
    - -link n搜索应用次数为n的文件: find -link 2
    - find -regex+完整路径 完全匹配
  - 层级查找
    - -maxdepth level 最大层级搜索
    - -mindepth level最小层级搜索 包括level
  - 数组查找
    - find -user username 查找指定用户的文件
    - find -group groupname查找指定属组文件
    - find -uid xxx查找指定uid文件
      - id username 查找id
    - find -nouser 查找没有用户的文件
    - find -nogroup 查找没有属组的文件
  - 文件类型 type
    - -d 目录
    - -f 文件
    - -l 符号链接
    - -s 套接字
    - -b 块设备
    - -c 字符设备
    - -p 管道文件
  - 空文件或空目录
    - -empty 查找空 find -empty
  - 条件 多个选项一起用
    - -a 与
    - -o 或
    - -not 非
    - 摩根定律
      - 非(A或B) 表示非A且非B
      - 非(A且B) 表示非A或非B
  - -path指定目录
    - find /etc/ -path ‘/etc/ssh' -prune -o -name “*.conf”
      - -prune 不打印 指定目录/etc/ssh
      - -path xx -prune -a -path xx2 -prune 排除多个目录,不打印
    - find -path /xx/xx 指定目录排除
  - -size 大小 find -size 2M
    - 2M 表示区间(#-1,#]
    - -2m 表示区间[0, #-1]
    - +2M 表示区间(#, ∞)
  - 时间  当前时间为0天
    - -atime #表示区间 [#,#+1)天数 
    - -atime -# 表示区间 (0,#) 天数以内
    - -atime +# 表示区间(#,∞)天数以外
    - -mtime
    - -ctime
    - 分钟为单位:
      - -amin
      - -mmin
      - -cmin

  ##### 处理动作

  - -print 默认的处理动作,草牧场在屏幕
  - -ls 类似ll 长格式
  - -delete 删除查到文件
  - -fls file 结果长格式保存文件
  - -ok 跟命令,提示是否执行命令
    - find -size 2M -ok rm -rf {} \;
  - -exec 跟命令,查询结果执行命令,一次性处理
    - find -size 2M -exec rm -rf {} \;

  ##### xargs

  - echo file{1..500000} | xargs touch
  - 有的命令不支持管道
  - 命令参数过长使用
  - xargs将管道的数据逐个发给后面命令处理
  - 一般和find一起使用

#### linux 三剑客

	#### grep 处理文本 全局搜索正则输出行

- global search RE and print line document

- 格式

  ```shell
  grep [option] "模式" file 
  --color=auto 匹配行添加颜色
  -v 取反
  -i 不区分大小写
  -n 查找内容增加行号
  -c 打印匹配到的行数
  -o 只显示匹配的
  -q 静默模式
  -A n 查到结果多往下显示n行
  -B n 查到结果向上多显示n行
  -C n 查到结果上下多显示n行
  -e 或者 grep -e "xx1" -e "xx2" filename
  -E 扩展正则表达式
  -F 不使用正则表达式
  -r 递归
  -w 匹配整个单词
  echo $? 输出上一条命令的结果
  ```

  

#### awk

#### sed 处理文本





#### 正则表达式元字符

##### 字符匹配

- grep "[0-9]\\+"  + ?需要转义 
- . 任意单个字符
- []指定范围任意单字符
  - 字符顺序按ASCII码
- [^ ] 指定字符以外的单字符
- [:upper:]
- [:lower:]
- [:alpha:]
- [:alnum:]
- [:digit:]
- [:blank:]
- [:punct:] 标点符号

##### 匹配次数

- *表示任意次数
- .*表示任意多字符
- \? 表示0或1次
- \\+ 表示1次或多次
- \\{n\\} 表示n次
- \\{m..n\\} m到n次
- \\{n,\\} 表示至少n次
- \\{,n\\} 表示至多n次

##### 位置

- ^开头
- $结尾
- ^$ 空行

>  grep -v "^#" filename 显示不以#开头,不是空行的

##### 向后应用

-  \1 分组匹配第一个分组,后面再匹配一次
- \n 分组匹配第n个分组,后面再匹配一次

##### 扩展正则表达式 -E

- 与正则表达式的区别是不需要来转义



#### 压缩

##### gzip/ gunzip

```shell
gzip [option] file 压缩成.gz文件
-c 保留原文件
-1 ~ -9 指定压缩比,默认-9  生成gz1-gz9
-d 解压
zcat 查看压缩文件内容 
zcat filename.gz > file 写入文件
```

##### bzip2 /bunzip2解压

```shell
bzip [options] file 压缩成.bz2文件
-d 解压
-k 保留原文件
-1 ~ -9 解压等级
bzcat 查看压缩文件内容
```

##### xz/ unxz解压

```shell
xz [option] file 压缩成.xz文件
-d 解压
-k 保留原文件
-1 ~ -9 解压等级
xzcat 查看压缩文件内容
```

##### zip/ unzip

```shell
zip [option] file 压缩成.zip文件

zip -r file 

```

##### tar 

- 归档工具,默认不压缩

```shell
tar [option] file 压缩为.tar文件
tar cpvf file 
c 创建
v 显示过程
p 把原来的属性带过来
f 指定归档文件
x 解压
-t 列出所有文件
tar -t -f xx.tar xxx 查看归档文件内容
tar -r -f xx.tar xxx 追加xxx文件到压缩包
tar xf xx.tar 解压
tar xf xx.tar -C /xx/ 解压指定目录

tar -z 使用 gzip压缩
tar -j 使用 bzip压缩
tar -J 使用 xz压缩

--exclude 排除文件目录
tar Jcf xx.tar.xz --exclude /排除目录/ /xx
tar Jxf xx.tar.xz

```



##### 分卷压缩

```shell 
split -b xx.tar.gz xxx 
-d 数字命名, 默认字母
-a 指定后后缀几位
cat 合并分卷
cat xx.tar.gz00[1-9] > xx.tar.gz
```



split -d -b 2M xx.tar.gz

-d 数字命名 默认
指定分卷大小2M压缩