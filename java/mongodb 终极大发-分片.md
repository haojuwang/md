MongoDB 终极大发-分片
## MongoDB 分片简介
* 1，分片指的就是把数据拆分，将其分散到不同机器上的过程。MongoDB 支持自动分片，
    对应用而言，好像始终和一个单机的服务器交互一样。

* 2，分片是一种在多台机器间分配数据的方法，
MongoDB使用分片来支持具有非常大的数据集和高吞吐量操作的部署。

#### ## MongoDB 分片集群

* 1，分片(shard)： 每个分片包含整体数据的一部分， 每个分片可以部署为replica set（副本集）。

* 2，mongos: mongos 作为查询路由器，是客户端和分片集群之间的桥梁。

* 3，配置服务器(config servers)：配置服务器保存着元数据和集群的配置，
    从mongo3.2版本开始 配置服务器要配置成replica set（副本集）。

![](img/set/sharded-cluster-production-architecture.png)    



#### mongodb 分片和复制的区别
    复制是让多台服务器拥有相同的数据副本。
    分片是每个分片都拥有整个数据集的一个子集，且相互不同的数据，
    多个分片的数据合起来构成整个数据集。

## Mongos命令
    用来执行客户端访问集群数据的**路由** ，它维护着一个内容列表，记录了每个分片包含的数据，
    应用程序只要连接上它，就跟操作单台服务器一样 。

## 配置服务器
    配置服务器就是普通的mongodb 服务器，保存整个集群和分片的元数据：集群中有哪些分片，分片的是哪些集合，以及数据块的分布。它极其重要，必须启用日志功能。

在大型的集群中，建议配置3台配置服务器，就足够用。

#### 启动配置服务器的方式：

* 1，先创建几个存放数据的文件夹，在mongodb文件夹中创建dbs文件夹，然后在dbs下面创建confdb文件夹，然后在confdb下面创建confdb1,confdb2,confdb3文件夹;在mongodb文件夹中创建logs文件夹，
在logs文件夹中创建文件conflog1 conflog2 conflog3。

```
mkdir dbs
cd dbs
mkdir confdb1 confdb2 confdb3 


mkdir logs
cd logs
touch conflog1 conflog2 conflog3


```

* 2,分别启动这三个配置服务器，使用--configsvr 指明是配置服务器：

```
./mongod --configsvr --dbpath ../dbs/confdb1 --logpath ../logs/conflog1 --fork --port 30001  --replSet cfg


```

* 3,--configsvr默认的端口为27019，默认的数据目录为/data/configdb，可以使用--dbpath和--port自己定义。

#### 启动副本集

```
    服务器一：
    ./mongod --dbpath ../dbs/db4 --logpath ../logs/log4 --port 40001 --fork --replSet rep --shardsvr

    ./mongod --dbpath ../dbs/db5 --logpath ../logs/log5 --port 40002 --fork --replSet rep --shardsvr

    ./mongod --dbpath ../dbs/db6 --logpath ../logs/log6 --port 40003 --fork --replSet rep --shardsvr


   服务器二：
    ./mongod --dbpath ../dbs/db1 --logpath ../logs/log1 --port 20001 --fork --replSet myrep --shardsvr

    ./mongod --dbpath ../dbs/db3 --logpath ../logs/log3 --port 20003 --fork --replSet myrep --shardsvr

    ./mongod --dbpath ../dbs/db2 --logpath ../logs/log2 --port 20002 --fork --replSet myrep --shardsvr

```

初始化副本集
```
 服务器一：
 ./mongo 10.211.55.3:20001
rs.initiate({_id:"myrep",members:[
    {_id:0,host:'10.211.55.3:20001'},
    {_id:1,host:'10.211.55.3:20002'},
    {_id:2,host:'10.211.55.3:20003}
    ]
})



服务器二：
./mongo 10.211.55.4:40001

rs.initiate({_id:"rep",members:[
    {_id:0,host:'10.211.55.4:40001'},
    {_id:1,host:'10.211.55.4:40002'},
    {_id:2,host:'10.211.55.4:40003}
    ]
})


```



#### mongos 命令启动
    Mongos会向所有的3台配置服务器发送写请求，并确保3台服务器拥有相同的数据。

```
此处只配置了一个配置服务器
./mongos --configdb cfg/10.211.55.4:30001 --logpath ../logs/conflog/mongoslog --logappend --fork

```

* 1，可以启动任意多个mongos,通常是一个应用服务器使用mongos,也就是说mongos 通常与应用服务器运行在一个机器上。

* 2，mongos 的默认端口是27017，可以用chunkSize来指定块的大小，默认是200M

## 将副本集转换为分片

* 1，如果没有副本集，按照前面的文章创建并初始化一个：如果有一个副本集，就打开相应的服务器，把副本集运行起来。


* 2，use admin 也就是切换到使用admin 的数据库。

* 3，然后连接到mongos，把副本集转换成为分片。

```
./mongos 10.211.55.3:27017
use admin


sh.addShard("myrep/10.211.55.3:20001")

sh.addShard("rep/10.211.55.4:40001")


```
不用把所有副本集的成员都写出来，mongos会自动检查整个副本集。副本集的名称myrep就用作了分片的名称。

* 4,使用sh.status();察看状态，会发现整个副本集里面的服务都加入进来了。
```
 sharding version: {
    "_id" : 1,
    "minCompatibleVersion" : 5,
    "currentVersion" : 6,
    "clusterId" : ObjectId("5912724d75483dc532bee8d4")
}
  shards:
    {  "_id" : "myrep",  "host" : "myrep/10.211.55.3:20001,10.211.55.3:20002",  "state" : 1 }
    {  "_id" : "rep",  "host" : "rep/10.211.55.4:40001,10.211.55.4:40002,10.211.55.4:40003",  "state" : 1 }
  most recently active mongoses:
    "3.4.7" : 1
 autosplit:
    Currently enabled: yes
  balancer:
    Currently enabled:  yes
    Currently running:  no
        Balancer lock taken at Wed May 10 2017 09:52:13 GMT+0800 (CST) by ConfigServer:Balancer
    Failed balancer rounds in last 5 attempts:  0
    Migration Results for the last 24 hours: 
        No recent migrations
  databases:
    {  "_id" : "leyue",  "primary" : "rep",  "partitioned" : true }
        leyue.users
            shard key: { "userId" : 1 }
            unique: false
            balancing: true
            chunks:
                rep 3
            { "userId" : { "$minKey" : 1 } } -->> { "userId" : "a1" } on : rep Timestamp(1, 1) 
            { "userId" : "a1" } -->> { "userId" : "xx" } on : rep Timestamp(1, 2) 
            { "userId" : "xx" } -->> { "userId" : { "$maxKey" : 1 } } on : rep Timestamp(1, 3) 

```

shards 字段表示是已加入分片的副本集



## 分片索引(Shard Keys)
    MongoDB使用分片索引对collection进行分区,文档根据不同的分区存放到不同的collection。
    分片索引(Shard Keys)是由每个文档都存在的字段或者字段组 组成。

* 1，一旦选择索引后，就不能更改了。

* 2，如果是非空collection索引必须存在，如果是空collection MongoDB 会给collection创建该索引。

* 3，分片的选择会影响分片集群的性能，效率和可扩展性。


## 块(Chunks)
    MongoDB将分片数据分割成块。每个块(Chunks)都是一个区间。

* 1,MongoDB通过平衡器(balancer)移动每个块的数据，使每个块的数据均匀分布。

* 2,MongoDB 将文档分组成为块，每个块由给定片键特定范围内的文档组成，一个块只存在于一个分片上，所以MongoDB 用一个比较小的表就能维护块和分片的映射关系。

* 3，块与块间的数据不能重复，因此不能使用数据来作为片键，因为MongoDB会为数据创建多个索引条目，从而导致同一数据在多个块中出现。

* 4，块是逻辑概念，而非物理存储实现。

#### 查看库的信息
块信息保存在config库chunks集合中，sh.status() 里面也带有分块的信息。
如果单个分片键可能重复的话，可以创建复合分片键，方式跟创建复合索引一样。

#### 拆分块
    mongos 会记录每个块中插入了多少数据，如果一个块的数据大小超出了块的大小，或者达到某个阈值（拆分点），MongoDB 会自动拆分这个块，








































