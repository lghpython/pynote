## redis数据库

默认端口:6379

### 下载安装

```shell
安装epel源 
yum install -y epel-release
安装redis
yum -y install redis  
编译安装
$ wget http://download.redis.io/releases/redis-6.0.4.tar.gz
$ tar xzf redis-6.0.4.tar.gz
$ cd redis-6.0.4
$ make
```

### 常用命令

- 连接

  ```shell
  ./src/rediscli
  -h ip地址
  -p 端口
  -s 套接字
  -a 密码
  -n 指定数据库的序号
  
  ```

- 命令相关

  ```shell
  redis命令不区分大小写
  ping 测试redis是否连通,返回pong
  info cpu
  echo 打印内容 测试使用
  quit 退出
  select 2 切换库,总共16个(index 0-15)
  del 删除指定或多个key
  exist key 判断key是否存在
  
  expire key second 指定key存活秒数 过期删除
  ttl 查看key的存活时间(秒) -2 key不存在 -1 key永久失效
  
  keys pattern 列出符合key,支持通配符
  
  move key db 移动key到指定库 成功返回1
  
  pexpire key 毫秒 : 指定key存活毫秒数
  pttl 查看key的存活时间 (毫秒)
  
  randomkey 随机获取一个key值
  rename key newkey 重命名key newkey存在则覆盖 key不存在则报错
  renamenx key newkey 重命名 key不存在报错 newkey存在不做操作
  
  type 查看key对应value的数据类型
  
  get key
  set key value
  ```

### NoSQL --not only sql

#### 分类

- 键值数据库  redis
  - 适用场景: 存储用户信息
- 面向文档数据库 MongoDB
  - 适用场景: 日志、分析
- 列存储数据库 Cassandra、HBase(大数据分析)
  - 适用场景:日志、博客平台
- 图数据库 Neo4J

#### NoSQL优势

- 传统数据遇到性能瓶颈
- 高并发读写需求

### redis数据类型

| 类型       | 说明                  | 作用                                                         | 用途   |
| ---------- | --------------------- | ------------------------------------------------------------ | ------ |
| string     |                       |                                                              |        |
| Hash       |                       |                                                              |        |
| List       |                       |                                                              |        |
| Set        | 哈希表实现,元素不重复 | 1.添加、删除,查找复杂度对视O(1) 2. 为集合提供求交集、并集和差集等操作 |        |
| Sorted Set |                       |                                                              | 排行榜 |

#### redis数据类型操作命令

##### string

----

 - set 
    - 设置key value 
    - 如果key存在,则覆盖,不存在新建
   - ex 5 设置key存活秒数
   	- px 2000 设置存活毫秒数
   	- nx 如果key不存在新建,存在不操作
   	- xx 只有key存在才操作
 - get : 获取key对应的value 只能获取一位key 
 - mset : 批量创建key value, 覆盖已存在的key
	- mget: 批量获取key的值 不存在返回nil
 - getset: 给指定的key设置value并返回原来的值,不错;在返回nil
- strlen: 返回指定key的value长度
- append: 如果key存在追加,不存在新建
- incr: key存在数值+1
- desr: key存在数值-1
- incrby: key存在数值加指定值
- desrby:key存在数值减指定值
-  getrange: 切片无步长,key的值返回索引范围
- incrbyfloat: key存在数值加指定小数(默认保留17位)
- decrbyfloat:key存在数值减指定小数 

##### list

****

- lpush 将一个或多个元素逐个插入列表的头部 
- lrange 切片,无步长 0,-1返回全部
- lpop 移除并返回列表第一个元素
- rpush: 将一个或多个value插入列表的末端
- rpop 移除并返回列表最后一个元素
- rpushx :value插入list尾部, key必须存在
- lpushx:value插入list头部, key必须存在
- lindex: 从头开始数获取下标对应值
- linsert: 插入指定值的前后位置
  - before: linsert key before v1 v2 插入v2到v1前,v1 不存在不操作
  - after
- llen: 获取列表长度
- lrem: 删除value
  - count大于0 从头开始数 删除count个
  - count小于0 从尾开始数
  - count等于0 全部删除
- lset: 替换指定索引的value, 索引超出报错
- ltrim: 列表切片

##### hash---值为字典

> {'db':{'redis':'redis.conf', 'mysql': 'my.cnf'}}

- hset: 给hash增加key value
- hlen: 长度
- hget: 获取
- hgetall:获取所有
- hmset:批量增加
- hmget: 批量获取
- hsetnx: 存在不操作,不存在添加
- hkeys:获取哈希所有的field
- hvals:获取哈希表所有的value
- hdel :删除一个过多个的field
- hexists: 判断是否存在,1存在,0不存在
- hincrby: 哈希表中的field的数值添加指定值
- hincrbyfloat: 哈希表中的field的数值添加小数

##### set

- sadd: 给集合添加值,存在不操作
- smembers:列出所有值
- smove: 移动值
  - 值存在移动,不存在不操作
  - 目标集合存在移动,不存在新建并移动
- spop: 随机删除指定个数,并打印
- srem: 删除指定一个或多个值
- srandmembers: 随机获取
  - 不指定个数默认随机取一个
  - 大于0 随机取出指定个数,大于总数去除全部,
  - 小于0 随机取指定个,会重复
- scard: 获取值的数量
- sdiff: 两个集合的差集
- sinter: 交集
- sunion: 并集
- sismember: 判断是否存在值,1 存在,0 不存在

##### sorted set 有序集合

- zadd: 添加
- zrange: 
- zrem
- ...

#### 主从配置

##### 发布订阅 

- subscribe:订阅一个到多个频道
- publish:发送消息
- psubscribe: 订阅指定模式的频道 *代表所有
- unsubscribe: 取消订阅
- pubsub 
  - pubsub channels查看当前活跃的频道
  - pubsub numsub channels 查看当前订阅人数

##### redis配置文件

```shell
grep -v '^$' redis.conf | grep -v '^#'
配置文件 
bind 127.0.0.1
protected-mode yes
port 6379
tcp-backlog 511
timeout 0
tcp-keepalive 300
daemonize no
supervised no
pidfile /var/run/redis_6379.pid
loglevel notice
logfile /var/log/redis/redis.log
databases 16
save 900 1 # 900s 1次更新 持久化
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir /var/lib/redis
slave-serve-stale-data yes
slave-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
repl-disable-tcp-nodelay no
slave-priority 100
appendonly no
appendfilename "appendonly.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes

```

##### auth

密码认证

#### redis的主从

```shell
## 拷贝一份配置文件(去空行和#号开头
grep -v '^$' redis.conf | grep -v '^#' >> redis6380.conf
## 修改端口、slaveof、ip地址、
## 添加master的密码
config set masterauth password
## 以新配置启动redis
redis-server  redis6380.conf
## 查询程序的端口
ss -tnlp| grep redis
ps  -ef | grep redis
```

- 基于异步的,平均每秒钟从服务器都会主服务器发送复制的情况
- 一个主多个从
- 从也可以有从服务器
- 复制功能不会阻塞主/从服务器

###### redis的持久化

- rdb : 

  ```shell
  优点:
    生成二进制文件
    配置文件 默认多长时间保存1次
    直接手动保存
    制作快照
    用作备份
    适合做灾难恢复
    主进程会fork一个子进程,用来复制保存数据
  缺点:
  	如果所数据需要尽量保存下来,不适合rdb
  	数据量大的时候,对系统消耗过大
  	
  save 900 1 # 900s 1次更新 持久化
  save 300 10
  save 60 10000
  stop-writes-on-bgsave-error yes
  rdbcompression yes
  rdbchecksum yes
  dbfilename dump.rdb # 数据保存文件
  dir /var/lib/redis # 保存目录
  ```

- aof

  ```shell
  ## 设置
  appendonly yes # 生成appendonly.aof
  appendfilename "appendonly.aof"
  appendfsync everysec
  ## redis服务
  ps -df | grep redis # 查询服务端口
  kill -9 端口号 # 杀进程
  redis-server redis.conf # 启动
  
  # 清空所有
  flushall 
  flushdb
  # 保存
  save
  
  优点:
  	持久化更好
  	aof将所有的操作都追加到一个文件, redis-check-aof
  	文件易读
  缺点
  	文件越来越大
  	aof的持久化速度比rdb慢,aof使用fsync
  	文件易读
  
  ```

  - 命令切换rdb 到 aof

    ```shell
    AUTH xxx
    info 
    config get appendonly
    ## rdb 转换 aof
    config set appendonly yes
    config set save ""
    
    ```

  ##### redis高可用

  ```shell
  高可用指服务器可以正常访问的时间,(99.9%, 99.99% 99.999%,等等)
  持久化---- rdb/aof
  复制---- slaveof 缺点:故障恢复无法自动化
  哨兵---- 
  集群
  ```

  - 哨兵 Sentinel: 一个redis 主/从服务器,各分配一个哨兵, 互相监控
    - 主观下线: 哨兵监测不到服务
    - 客观下线: 所有哨兵统一协商认证,故障自动切换

#### 集群 redis cluster

- redis-cluster 应用大公司,数据量特别大时
- 两种分布式方案
  - 客户端分区方案
  - 代理分区方案 twemproxy/ nutcraker、codis 
- 查询路由方案
- redis数据分区

```shell
## 16384槽位 虚拟槽分区
## 最少需6台服务器 六个配置文件 6个节点
# 
mkdir /redis
cd /redis/
vim redis6380.conf
# redis配置文件启动cluster
## start
bind 127.0.0.1
port 6380
daemonize yes
pidfile 6380.pid
logfile 6380.log
cluster enabled yes
cluster-config-file node-6380.conf
cluster-node-tmeout 100000
## end
# 更改6380 -> 6381 另存为redis6381.conf
sed -i 's/6380/6381/g' redis6381.conf 
# 依次更改6382 /6383/ 6384 /6385

## 集群管理 redis-trib.rb需要ruby环境


```

- 集群 环境准备

  - 虚拟槽

    - 槽位16383个槽位
    - 分配算法: slot=CRC16(key)&16838

  - 安装ruby

    ```shell
    ## 2.2版以上
    wget xx
    tar xf xx
    cd ruby-2.6.4
    ./configure --prefix=/opt/ruby
    make && make install 
    ## 配置环境变量
    PATH =/opt/ruby/bin:$PATH
    source 
    ```

  - 安装redis依赖

    ```shell
    gem install redis
    ```

  - 安装命令

    ```shell 
    redis-cli --cluster help
    - create # 创建
    - check  # 检查集群
    - info 	# 查看集群信息
    - fix  	# 修复集群
    ## 启动6个服务器
    redis-server /redis/redis6380.conf
    redis-server /redis/redis6381.conf
    redis-server /redis/redis6382.conf
    redis-server /redis/redis6383.conf
    redis-server /redis/redis6384.conf
    redis-server /redis/redis6385.conf
    ## 新建集群 redis-cli
    redis-cli --cluster create --cluster-replicas 1 127.0.0.1:6380 127.0.0.1:6381 ....
    ## 新建集群 redis-tib.rb 管理工具
    redis-tib.rb create --replicas 1 127.0.0.1:6380 127.0.0.1:6381 ....
    
    ```

  - 连接命令

    ```shell
    redis-cli -c -p 6380 
    # -c 声明是集群
    # -p 指定端口
    ```

  

### python应用

python模块安装: pip3 install redis

```python
import redis
r = redis.StrictRedis(host="172.16.107.128",password="xxx",decode_response=True )
# 监听分机ip地址
## r=redis.Redis() 向后兼容老版本
r.set("key","val")
r.keys()


import rediscluster
nodes=[{"host":"192.168.21.128",'port':6380},{...},{...}...,{...}]
c = rediscluster.RedisCluster(start_ups=nodes,decode_response=True)
#c=rediscluster.RedisCluster(host="172.16.107.128",decode_response=True)
print(c.get('name'))

```



```python
import redis
# 直接连接redis
conn = redis.Redis(host='xx.xx.xx.xx', port=6379, password='xxx', encoding='utf-8')
# 设置键值：15131255089="9999" 且超时时间为10秒（值写入到redis时会自动转字符串）
conn.set('15131255089', 9999, ex=10)
# 根据键获取值：如果存在获取值（获取到的是字节类型）；不存在则返回None
value = conn.get('15131255089')
```

每次操作redis如果都重新连接一次效率会比较低,使用连接池

```python
# 创建redis连接池（默认连接池最大连接数 2**31=2147483648）
pool = redis.ConnectionPool(host='xx.xx.xxx.xx', port=6379, password='xxx', encoding='utf-8', max_connections=1000)
# 去连接池中获取一个连接
conn = redis.Redis(connection_pool=pool)
```

