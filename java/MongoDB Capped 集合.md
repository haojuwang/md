MongoDB Capped 集合

#### Capped集合简介
* 1，Capped集合是一个固定大小，高性能的，文档按照插入顺序的一个集合。
	新的对象会把覆盖旧的对象，像环形缓存一样。

* 2，find时默认就是插入的顺序,Capped集合会自动维护。

* Capped 集合用来解决top 多少的问题，最新的top条评论，最活跃的top账号...

#### Capped集合创建语法	
	db.createCollection("集合名称", { capped : true, size : num, max : num } )

	db.createCollection("log", { capped : true, size : 1000, max : 5 } )

* 1, size用来指定集合大小，单位KB。

* 2, 限制集合中对象的个数：可以在创建时设置max参数。

* 3, 指定mac数量的时候必须同时指定size容量。淘汰机制只有在容量还没有满时才会依据文档数量工作。
	要是容量满了，淘汰机制会依据容量来工作。

```
db.log.insert({"name":"u1"})
db.log.insert({"name":"u2"})
db.log.insert({"name":"u3"})
db.log.insert({"name":"u4"})
db.log.insert({"name":"u5"})
db.log.find()
{ "_id" : ObjectId("598d6cacde67b24068234115"), "name" : "u1" }
{ "_id" : ObjectId("598d6cb0de67b24068234116"), "name" : "u2" }
{ "_id" : ObjectId("598d6cb4de67b24068234117"), "name" : "u3" }
{ "_id" : ObjectId("598d6cb8de67b24068234118"), "name" : "u4" }
{ "_id" : ObjectId("598d6cbcde67b24068234119"), "name" : "u5" }

//再插入一个数据 将会覆盖掉第一条数据
db.log.insert({"name":"u6"})
db.log.find()
{ "_id" : ObjectId("598d6cb0de67b24068234116"), "name" : "u2" }
{ "_id" : ObjectId("598d6cb4de67b24068234117"), "name" : "u3" }
{ "_id" : ObjectId("598d6cb8de67b24068234118"), "name" : "u4" }
{ "_id" : ObjectId("598d6cbcde67b24068234119"), "name" : "u5" }
{ "_id" : ObjectId("598d8faede67b2406823411a"), "name" : "u6" }


```


#### 查看一个集合是不是Capped集合
	db.collection.isCapped()

```
	db.log.isCapped()
	true  //false 不是Capped集合
```	

#### 把一个集合转换成Capped集合，反之不行
	db.runCommand({"convertToCapped": "要转换的集合", size: 100000});
	db.runCommand({"convertToCapped": "mycoll", size: 100000});	

#### 注意事项
* 1，32位机器上，一个Capped集合的最大值约为482.5M,64位只受系统文件大小的限制，
	创建的时候，可以预指定大小。

* 2，可以向Capped 集合中加入数据，但不能删除数据，也不能改变集合大小。

* 3，可以使用drop 方法删除集合，删除后，需显示的重新创建这个集合。
```
	db.log.drop()
	true
	
	db.createCollection("log", { capped : true, size : 1000, max : 5 } )
	{ "ok" : 1 }

``` 	




































