## mongodb文档存储特点
将文档插入到MongoDB的时候,文档是按照插入的顺序,依次在磁盘上相邻保存 
因此,一个文档变大了,原来的位置要是放不下这个文档了,
就需要把这个文档移动到集合的另外一个位置,通常是最后,能放下这个文档的地方。

##### 示例： 修改第一个文档使它变大它会移动到最后
```
	 db.userdatas.find()
{ "_id" : ObjectId("597f357a09c84cf58880e410"), "name" : "u5", "age" : 78 }
{ "_id" : ObjectId("597f357a09c84cf58880e412"), "name" : "u3", "age" : 32 }
{ "_id" : ObjectId("597f357a09c84cf58880e411"), "name" : "u4", "age" : 30, "score" : [ 7, 4, 2, 0 ] }
{ "_id" : ObjectId("597fcc0f411f2b2fd30d0b3f"), "age" : 20, "score" : [ 7, 4, 2, 0, 10, 9, 8, 7 ], "name" : "lihao" }
{ "_id" : ObjectId("597f357a09c84cf58880e413"), "name" : "u2", "age" : 33, "wendang" : { "yw" : 80, "xw" : 90 } }
{ "_id" : ObjectId("5983f5c88eec53fbcd56a7ca"), "date" : ISODate("2017-08-04T04:19:20.693Z") }
{ "_id" : ObjectId("597f357a09c84cf58880e40e"), "name" : "u1", "age" : 26, "address" : "中国砀山" }
{ "_id" : ObjectId("597f357a09c84cf58880e40f"), "name" : "u1", "age" : 37, "score" : [ 10, 203, 12, 43, 56, 22 ] }


 db.userdatas.update({"name":"u5"},{"$set":{"address":"china beijing chaoyang"}})


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

文档name="u5" 会移动文档的最后


#### MongoDB进行文档移动是非常慢的
	移动文档的时候,MongoDB需要将文档原先占用的空间释放掉,然后将文档写入新的空间,
	相对费时,尤其是文档比较大,又频繁需要移动的话,会严重影响性能。

* 在插入的时候可以插入一个不用的字段来占位置。





























