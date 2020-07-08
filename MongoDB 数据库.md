## MongoDB 数据库

为web应用提供可扩展的高性能数据存储,将数据存储为文档,基于分布式文件的存储

NoSQL ( Not only SQL ): 用于大规模存储

默认端口: 27017

特点:

- 面向文档的存储
- 可设置任何值为索引,快速排序搜索
- 负载增加,分片操作,分布到其他的节点
- 查询表达式,用JSON,可内嵌对象、数组
- update()命令可实现替换完成
- Map/reduce 批量处理、聚合操作
- GridFS 内置功能: 存放大量小文件
- 语序服务端执行脚本,自定义便携js函数

监控工具(插件)

- Munin: 网络系统监控

- Gangila: 高性能系统监视工具

- Cacti: 检测CPU负载、网络带宽利用率

一个MongoDB可建多个数据库,默认数据库db,位于data 目录中

保留数据库: admin、 local、 config

文档:键值对(BSON:二进制的JSON)

- 键值对是有序的
- 区分大小写
- 键是唯一的,字符串类型,可以是任意UTF-8

集合: capped collections 固定大小的collection

```python
db.createCollection("mycol",{capped: true,size:100000})
```

元数据: 

系统命名空间

>  db.name.system.*  
>
> *号代表一个集合: namespaces、 indexes、profile、users、sourses

mongo数据类型

| 数据类型     | 说明                    |
| ------------ | ----------------------- |
| String       | 字符串                  |
| Integer      | 整型                    |
| Boolean      | 布尔值                  |
| Double       | 双精度浮点型            |
| Min/Max keys | 当前值与最小/最大作比较 |
| Arrays       | 数组                    |
| Timestamp    | 时间戳                  |
| Object       | 内嵌文档                |
| Null         | 空置                    |
| Symbol       | 符号                    |
| Date         | 日期                    |
| Object ID    | 对象ID                  |
| Binary Data  | 二进制数据              |
| Code         | 代码                    |
| RE           | 正则表达式              |

mongoDB安装与使用

- 启动服务:bin目录执行mongod
- 默认端口: 27017, mongoDB web界面: localhost: 28017
- shell连接MongoDB服务: mongoldb://localhost
- 用户名密码连接: mongodb://username:password@localhost
- 连接多台服务器: 分片 replica Set
  - mongodb://localhost,localhost:27019,localhost:27018
  - mongoldb://host1,host2,host3/?slaveOk=True # 写操作应用在主服务器,并分布查询到从服务器
  - mongoldb://host1,host2,host3/connect=direct:slaveOk=true

标准格式:

```
mongoldb://[username:password@]host1[:port][,host2[:port2]... [,hostN[:portN]]][/[database][?options]
```

| 参数                |      | 说明                                                         |
| ------------------- | ---- | ------------------------------------------------------------ |
| replicaSet=name     |      | 验证replica set名称 Impliesconnect=replicaSet                |
| slaveOk=true\|false |      | true:在connect=direct模式下,驱动会之间连第一台机器;在connect=replicaSet模式下,驱动会发送所有的写请求到主服务器并把读取操作分布在其他从服务武器 |
| safe=true\|false    |      | true:在执行更新操作之后,驱动会自动发送getLastError命令来确保更新成功 |
| w=n                 |      | 驱动添加{w:n}到getLastError命令,应用于safe=true              |
| wtimeoutMS=ms       |      | 驱动添加{wtimeoutMS: ms}到getLastError命令,应用于safe=true   |
| fsync= true\|false  |      | true: 驱动添加{fsync: true}到getlasterror命令,应用于safe=true |
| journal=true\|false |      | true,同步到journal.应用于safe=true                           |
| connectTimeoutMS=ms |      | 可以打开连接的时间                                           |
| socketTimeoutMS=ms  |      | 发送接收socket的时间                                         |

### 数据库操作

1. 创建数据库(存在跳转到指定数据库): use DATABASE_NAME

2. 查询数据库: show dbs

3. 删除当前数据库: db.dropDatabase( )

4. 删除集合: db.collection.drop( )

5. 文档变量: document= ({...})

6. 插入文档: db.COLLECTION_NAME.insert(document)

7. update( )更新数据库: db.collection.update(criteria,objNew,upsert,multi)

   1. criteria: 查询条件,键值对
   2. objNew: update的对象和一些更新操作符
   3. upsert: true: 不存在插入记录,false:反之
   4. multi: true: 更新所以能查询到的,false: 更新第一条记录
   5. 修改器:
      - $set: 更新操作
      - $unset: 删除操作
      - $inc: 增加操作
      - $push: 类似append
      - $pushAll : 类似extends
      - $pull: 指定删除
      - $pullAll: 批量列表删除
      - $pop: 删除第一个(-1)/最后一个(1)

8. 查询: db.col.find( ) 格式化: db.col.find( ).pretty( )

9. 操作符:

   1. 等于: {<key> : <value>}

   2. 小于: {<key>:{$lt: <value>}

   3. 小于等于: {<key>:{$lte: <value>}

   4. 大于: {<key>:{$gt: <value>}

   5. 大于等于: {<key>:{$gte: <value>}

   6. 不等于: {<key>:{$ne: <value>}

   7. AND 条件: 以逗号分隔 {<key> : <value>,<key> : <value>}

   8. OR 条件: $or为键,值为列表{$or: [{<key> : <value>},{<key> : <value>}]}

   9. $type操作符: 

      | 类型                   | 数字      |
      | ---------------------- | --------- |
      | Double                 | 1         |
      | String                 | 2         |
      | Object                 | 3         |
      | Array                  | 4         |
      | Binary data            | 5         |
      | Undefined              | 6(废弃)   |
      | Object id              | 7         |
      | Boolean                | 8         |
      | Date                   | 9         |
      | Null                   | 10        |
      | RE                     | 11        |
      | JavaScript             | 13        |
      | Symbol                 | 14        |
      | JavaScript(with scope) | 15        |
      | 32-bit integer         | 16        |
      | TimeStamp              | 17        |
      | 64-bitInteger          | 18        |
      | Min key / Max key      | 255 / 127 |

      

10. 删除文档: db.collection.remove(<query>, { justOne: <boolean>, writeConcern:<document>})

    1. query: 可选) 条件
    2. justOne: 可选) 设true或1 ,只删除一个文档
    3. writeConcern: 可选) 抛出异常级别

### MongoDB 方法

1. limit(指定读取条数)方法: db.COLLECTION_NAME.find( ).limit(NUMBER)

2. skip(指定跳过条数)方法: db.COLLECTION_NAME.find( ).limit(NUMBER).skip(NUMBER)

3. sort()排序(1升序;-1降序):db.COLLECTION_NAME.find().sort({KEY:1})

4. ensureIndex()创建索引: db.COLLECTION_NAME.ensureIndex({KEY:1})  # 1升序创建,-1降序创建索引

5. aggregate()聚合方法: db.COLLECTION_NAME.aggregate(AGGREGATE_OPREATION)

   1. 聚合表达式:

      | 表达式    | 描述                     | 实例 |
      | --------- | ------------------------ | ---- |
      | $sum      | 计算总和                 |      |
      | $avg      | 计算平均值               |      |
      | $max      | 获取最小值               |      |
      | $min      | 获取最大值               |      |
      | $push     | 插入值到数组中           |      |
      | $addToSet | 杀入值到数组中不创建副本 |      |
      | $last     | 获取最后一个             |      |
      | $first    | 获取第一个               |      |

   2. 管道的概念,聚合框架常用的操作

      1. $project ----
      2. $match ----过滤数据
      3. $limit ---- 限制管道返回文档数
      4. $skip ---- 跳过指定数量文档返回余下文档
      5. $unwind ---- 数组拆分多条
      6. $group ---- 集中文档分组利于统计
      7. $sort ---- 排序输出
      8. $geoNear----输出接近地理位置的有序文档

      

### MongoDB复制(副本集):

1. 将数据同步的多个服务器,保证安全性

2. 主节点处理请求,从节点复制数据

3. 副本集设置

   - 指定--replSet启动MongoDB

     mongos --port "PORT" --dbpath "数据库路径" --replSet "新实例名"

     例: mongod --port "PORT" --dbpath "/data/db" --replSet rs0

   - 启动新副本集 : rs.initiate() 

   - 查看副本集配置: rs.conf()

   - 查看副本集状态: rs.status()

   - 副本集添加新成员: rs.add(host:port)



### python中的MongoDB应用

安装库: pymongo

