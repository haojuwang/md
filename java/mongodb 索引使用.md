mongodb 索引使用

## 作用
* 索引通常能够极大的提高查询。
* 索引是一种数据结构，他搜集一个集合中文档特定字段的值。
* B-Tree索引来实现。

## 创建索引
	db.collection.createIndex(keys, options)

#### keys
	keys由文档字段和索引类型组成。如{"name":1} 
	key 表示字段 value 1,-1  1表示升序，-1降序



#### options
	options 创建索引的选项。

| 参数        | 类型          | 描述  |
| ------------- |:-------------:| :-----|
| background      | boolean | 创建索引在后台运行，不会阻止其他对数据库操作 |
| unique      | boolean      | 创建唯一索引，文档的值不会重复 |
| name | string      |    索引名称，默认是：字段名_排序类型 开始排序 |
| sparse | boolean      |    过滤掉null，不存在的字段 |


## 查看索引
```
	db.collection.getIndexes()

```

```
	{
		"v" : 1,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "leyue.userdatas"
	},
	{
		"v" : 1,
		"key" : {
			"name" : 1 //索引字段
		},
		"name" : "name_1", //索引名称
		"ns" : "leyue.userdatas"
	}

```

## 删除索引
```
	db.collection.dropIndex(index) 删除指定的索引。

	db.collection.dropIndexes() 删除除了_id 以外的所有索引。

```
* index 是字符串  表示按照索引名称 name 删除字段。
* index 是{字段名称:1} 表示按照key 删除索引。


## 创建/查看/删除 示例
#### 查看数据
```
	 db.userdatas.find()
{ "_id" : ObjectId("597f357a09c84cf58880e412"), "name" : "u3", "age" : 32 }
{ "_id" : ObjectId("597f357a09c84cf58880e411"), "name" : "u4", "age" : 30, "score" : [ 7, 4, 2, 0 ] }
{ "_id" : ObjectId("597fcc0f411f2b2fd30d0b3f"), "age" : 20, "score" : [ 7, 4, 2, 0, 10, 9, 8, 7 ], "name" : "lihao" }
{ "_id" : ObjectId("597f357a09c84cf58880e413"), "name" : "u2", "age" : 33, "wendang" : { "yw" : 80, "xw" : 90 } }
{ "_id" : ObjectId("5983f5c88eec53fbcd56a7ca"), "date" : ISODate("2017-08-04T04:19:20.693Z") }
{ "_id" : ObjectId("597f357a09c84cf58880e40e"), "name" : "u1", "age" : 26, "address" : "中国砀山" }
{ "_id" : ObjectId("597f357a09c84cf58880e40f"), "name" : "u1", "age" : 37, "score" : [ 10, 203, 12, 43, 56, 22 ] }
{ "_id" : ObjectId("597f357a09c84cf58880e410"), "name" : "u5", "age" : 78, "address" : "china beijing chaoyang" }
```

#### 给字段name 创建索引 
```
	// 创建索引
	db.userdatas.createIndex({"name":1})

	{
		"createdCollectionAutomatically" : false,
		"numIndexesBefore" : 1,
		"numIndexesAfter" : 2,
		"ok" : 1
	}


	// 查看索引
	db.userdatas.getIndexes()

	[
		{
			"v" : 1,
			"key" : {
				"_id" : 1
			},
			"name" : "_id_",
			"ns" : "leyue.userdatas"
		},
		{
			"v" : 1,
			"key" : {
				"name" : 1
			},
			"name" : "name_1",
			"ns" : "leyue.userdatas"
		}
	]


```
#### 给字段name 创建索引并命名为myindex

```
	db.userdatas.createIndex({"name":1})

	db.userdatas.createIndex({"name":1},{"name":"myindex"})

	db.userdatas.getIndexes()
	[
		{
			"v" : 1,
			"key" : {
				"_id" : 1
			},
			"name" : "_id_",
			"ns" : "leyue.userdatas"
		},
		{
			"v" : 1,
			"key" : {
				"name" : 1
			},
			"name" : "myindex",
			"ns" : "leyue.userdatas"
		}
	]


```


#### 给字段name 创建索引 创建的过程在后台执行
	当mongodb 集合里面的数据过大时 创建索引很耗时，可以在放在后台运行。

```
	db.userdatas.dropIndex("myindex")

	db.userdatas.createIndex({"name":1},{"name":"myindex","background":true})

```	

#### 给age 字段创建唯一索引
```
	db.userdatas.createIndex({"age":-1},{"name":"ageIndex","unique":true,"sparse":true})

	db.userdatas.getIndexes()

[
	{
		"v" : 1,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "leyue.userdatas"
	},
	{
		"v" : 1,
		"key" : {
			"name" : 1
		},
		"name" : "myindex",
		"ns" : "leyue.userdatas",
		"background" : true
	},
	{
		"v" : 1,
		"unique" : true,
		"key" : {
			"age" : -1
		},
		"name" : "ageIndex",
		"ns" : "leyue.userdatas",
		"sparse" : true
	}
]

// 插入一个已存在的age
	db.userdatas.insert({ "name" : "u8", "age" : 32})


WriteResult({
	"nInserted" : 0,
	"writeError" : {
		"code" : 11000,
		"errmsg" : "E11000 duplicate key error index: leyue.userdatas.$ageIndex dup key: { : 32.0 }"
	}
})

```
#### 创建复合索引
```
	db.userdatas.createIndex({"name":1,"age":-1})

	db.userdatas.getIndexes()
[
	{
		"v" : 1,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "leyue.userdatas"
	},
	{
		"v" : 1,
		"key" : {
			"name" : 1,
			"age" : -1
		},
		"name" : "name_1_age_-1",
		"ns" : "leyue.userdatas"
	}
]

```








## 所有的字段都存在集合 system.indexes 中

```
db.system.indexes.find()
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "leyue.userdatas" }
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "leyue.scores" }
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "leyue.test" }
{ "v" : 1, "key" : { "user" : 1, "name" : 1 }, "name" : "myindex", "ns" : "leyue.test" }
{ "v" : 1, "key" : { "_id" : 1 }, "name" : "_id_", "ns" : "leyue.mycapped" }
{ "v" : 1, "key" : { "user" : 1 }, "name" : "user_1", "ns" : "leyue.test" }
{ "v" : 1, "key" : { "name" : 1 }, "name" : "myindex", "ns" : "leyue.userdatas" }

```
## 索引总结
* 1:创建索引时,1表示按升序存储,-1表示按降序存储。

* 2:可以创建复合索引,如果想用到复合索引,必须在查询条件中包含复合索引中的前N个索引列

* 3: 如果查询条件中的键值顺序和复合索引中的创建顺序不一致的话,
	MongoDB可以智能的帮助我们调整该顺序,以便使复合索引可以为查询所用。

* 4: 可以为内嵌文档创建索引,其规则和普通文档创建索引是一样的。

* 5: 一次查询中只能使用一个索引,$or特殊,可以在每个分支条件上使用一个索引。

* 6: $where,$exists不能使用索引,还有一些低效率的操作符,比如:$ne,$not,$nin等。	

* 7: 设计多个字段的索引时,应该尽量将用于精确匹配的字段放在索引的前面。


## explain 使用
#### 语法
```
	db.collection.explain().<method(...)>
```

explain() 可以设置参数 ：
* queryPlanner。

* executionStats。

* allPlansExecution。

#### 示例
```
for(var i=0;i<100000;i++) {
  db.test.insert({"user":"user"+i});
}
```

##### 没有使用索引
```
	db.test.explain("executionStats").find({"user":"user200000"})
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "leyue.test",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"user" : {
				"$eq" : "user200000"
			}
		},
		"winningPlan" : {
			"stage" : "COLLSCAN",
			"filter" : {
				"user" : {
					"$eq" : "user200000"
				}
			},
			"direction" : "forward"
		},
		"rejectedPlans" : [ ]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 2,
		"executionTimeMillis" : 326,
		"totalKeysExamined" : 0,
		"totalDocsExamined" : 1006497,
		"executionStages" : {
			"stage" : "COLLSCAN",
			"filter" : {
				"user" : {
					"$eq" : "user200000"
				}
			},
			"nReturned" : 2,
			"executionTimeMillisEstimate" : 270,
			"works" : 1006499,
			"advanced" : 2,
			"needTime" : 1006496,
			"needYield" : 0,
			"saveState" : 7863,
			"restoreState" : 7863,
			"isEOF" : 1,
			"invalidates" : 0,
			"direction" : "forward",
			"docsExamined" : 1006497
		}
	},
	"serverInfo" : {
		"host" : "lihaodeMacBook-Pro.local",
		"port" : 27017,
		"version" : "3.2.1",
		"gitVersion" : "a14d55980c2cdc565d4704a7e3ad37e4e535c1b2"
	},
	"ok" : 1
}

```
* executionStats.executionTimeMillis: query的整体查询时间。

* executionStats.nReturned: 查询返回的条目。

* executionStats.totalKeysExamined： 索引扫描条目。

* executionStats.totalDocsExamined： 文档扫描条目。



executionTimeMillis = 326  query 执行时间
nReturned=2 返回两条数据
totalKeysExamined=0 没有用到索引
totalDocsExamined 全文档扫描

理想状态：
	nReturned=totalKeysExamined & totalDocsExamined=0

##### Stage状态分析

|  stage        |  描述  |
| ------------- |:-------------|
| COLLSCAN      | 全表扫描 | 
| IXSCAN      | 扫描索引 | 
| FETCH  |根据索引去检索指定document
|SHARD_MERGE |将各个分片返回数据进行merge |
|SORT | 表明在内存中进行了排序 |
|LIMIT | 使用limit限制返回数|
|SKIP | 使用skip进行跳过|
|IDHACK | 针对_id进行查询|
|SHARDING_FILTER | 通过mongos对分片数据进行查询|
|COUNT | 利用db.coll.explain().count()之类进行count运算|
|COUNTSCAN | count不使用Index进行count时的stage返回|
|COUNT_SCAN |  count使用了Index进行count时的stage返回|
|SUBPLA | 未使用到索引的$or查询的stage返回|
|TEXT   | 使用全文索引进行查询时候的stage返回|
|PROJECTION  | 限定返回字段时候stage的返回|

* 对于普通查询，我希望看到stage的组合(查询的时候尽可能用上索引)：

	Fetch+IDHACK
	
	Fetch+ixscan
	
	Limit+（Fetch+ixscan）
	
	PROJECTION+ixscan
	
	SHARDING_FITER+ixscan
	
	COUNT_SCAN

* 不希望看到包含如下的stage：

COLLSCAN(全表扫描),SORT(使用sort但是无index),不合理的SKIP,SUBPLA(未用到index的$or),COUNTSCAN(不使用index进行count)




##### 使用索引
```
	db.test.createIndex({"user":1},{"name":"myindex","background":true})

	db.test.explain("executionStats").find({"user":"user200000"})
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "leyue.test",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"user" : {
				"$eq" : "user200000"
			}
		},
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"user" : 1
				},
				"indexName" : "myindex",
				"isMultiKey" : false,
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 1,
				"direction" : "forward",
				"indexBounds" : {
					"user" : [
						"[\"user200000\", \"user200000\"]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 2,
		"executionTimeMillis" : 0,
		"totalKeysExamined" : 2,
		"totalDocsExamined" : 2,
		"executionStages" : {
			"stage" : "FETCH",
			"nReturned" : 2,
			"executionTimeMillisEstimate" : 0,
			"works" : 3,
			"advanced" : 2,
			"needTime" : 0,
			"needYield" : 0,
			"saveState" : 0,
			"restoreState" : 0,
			"isEOF" : 1,
			"invalidates" : 0,
			"docsExamined" : 2,
			"alreadyHasObj" : 0,
			"inputStage" : {
				"stage" : "IXSCAN",
				"nReturned" : 2,
				"executionTimeMillisEstimate" : 0,
				"works" : 3,
				"advanced" : 2,
				"needTime" : 0,
				"needYield" : 0,
				"saveState" : 0,
				"restoreState" : 0,
				"isEOF" : 1,
				"invalidates" : 0,
				"keyPattern" : {
					"user" : 1
				},
				"indexName" : "myindex",
				"isMultiKey" : false,
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 1,
				"direction" : "forward",
				"indexBounds" : {
					"user" : [
						"[\"user200000\", \"user200000\"]"
					]
				},
				"keysExamined" : 2,
				"dupsTested" : 0,
				"dupsDropped" : 0,
				"seenInvalidated" : 0
			}
		}
	},
	"serverInfo" : {
		"host" : "lihaodeMacBook-Pro.local",
		"port" : 27017,
		"version" : "3.2.1",
		"gitVersion" : "a14d55980c2cdc565d4704a7e3ad37e4e535c1b2"
	},
	"ok" : 1
}

```
* executionTimeMillis: 0
* totalKeysExamined: 2
* totalDocsExamined:2
* nReturned:2
* stage:IXSCAN
* 使用索引和不使用差距很大，合理使用索引，一个集合适合做 4-5 个索引。


## 相关文章
http://www.mongoing.com/eshu_explain3
https://docs.mongodb.com/v3.2/reference/explain-results/#queryplanner




一次写 多次读的文件
db.fs.files.find()






















