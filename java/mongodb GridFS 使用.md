

## GridFS简介
* 1，GridFS是用于存储和检索超过16MB的BSON文档大小限制的文件的解决方案。	

* 2，GridFS是MongoDB用来存储大型二进制文件的一种存储机制。

* 3，GridFS 不是讲文件存储在单个文档中，而是将文件分为块，并将每个块作为单独的文档存储，
	默认情况下，GridFS使用的块大小为256kb,最后一个块除外。

* 4， GridFS 使用两个集合存储文件，一个集合文件的块，另一个存储文件的原始数据。

## GridFS 优缺点
#### 优点
* 1:能够简化技术栈,如果已经使用了MongoDB,那么使用GridFS,就不需要其它独
立的存储工具了

* 2:GridFS会自动平衡已有的复制,或者为MongoDB设置的自动分片,所以对文件存
储做故障转移或者是横向扩展会更容易 。

* 3:GridFS的功能不错,能自动解决一些其他文件系统遇到的问题,如在同一个目录
下存储大量的文件

#### 缺点
* 1: 性能较低，不如直接访问文件系统快。

* 2：无法修改文档。如果要修改GridFS里面的文档，只能是先删除再添加



## mongofiles 命令使用

### 语法
	mongofiles <options> <commands> <filename>

* mongofiles 的命令 和mongo  mongod 在同一级目录中。

#### 常用的 options  
|  option        |  描述  |
| ------------- |:-------------|
| --host      | mongodb地址 | 
| --port      | 端口号 | 
| --username      | 用户名 | 
| --password      | 密码 | 
| --db      | mongodb 数据库 | 
| --local      | 本地的文件名称 用于 put/get |
* 双 '-'

####  commands 命令
|  command        |  描述  |
| ------------- |:-------------|
| list <prefix>   | 列出GridFS存储的文件 以xxx 开头的文件| 
| search <string>   | 搜索 包含 xxx的文件 | 
| put <filename>  | 将本地文件保存于 GridFS| 
| get <filename>   | 下载文件 | 
| get_id "<ObjectId>" | 获取到指定id的文件 | 
| delete <filename> | 删除指定的文件 |
| delete_id | 删除指定id的文件 |

* 双 '-'

#### 上传一个文件到mongodb put
```
bin/mongofiles --port 20001 --host 127.0.0.1 -d dbs\db1 --local /Users/lihao/Downloads/cart2.jpg put cart.jpg

2017-08-13T20:22:21.586+0800	connected to: 127.0.0.1:20001
added file: cart.jpg

```
* -d 表示数据库

* --local 要上传的本地文件

* put 存储在mongodb 中的文件名

#### 查看存储的所有的文件 list
```
bin/mongofiles --port 20001 --host 127.0.0.1 -d dbs\db1 list

2017-08-13T20:26:45.426+0800	connected to: 127.0.0.1:20001
/Users/lihao/Downloads/rsa.txt	2777
rsa3.txt	2777
cart.jpg	21421

```


#### 搜索文件 search
```
bin/mongofiles --port 20001 --host 127.0.0.1 -d dbs\db1  search cart
2017-08-13T20:28:44.750+0800	connected to: 127.0.0.1:20001
cart.jpg	21421
```

#### 下载文件 get
```
bin/mongofiles --port 20001 --host 127.0.0.1 -d dbs\db1  --local  /Users/lihao/Desktop/rsa/cart.jpg get cart.jpg
2017-08-13T20:39:22.282+0800	connected to: 127.0.0.1:20001
finished writing to /Users/lihao/Desktop/rsa/cart.jpg

```

---local 下载到本地文件名全路径


#### 删除mongodb 存储文件
```
bin/mongofiles --port 20001 --host 127.0.0.1 -d dbs\db1  delete cart.jpg
2017-08-13T20:45:35.762+0800	connected to: 127.0.0.1:20001
successfully deleted all instances of 'cart.jpg' from GridFS
```



## GridFS 基本原理
	GridFS 会将大文件分割为多个块，将每个块作为独立的文档进行存储，
	另外用一个文档来讲这些块组织到一起，并存储该文件的元信息。


#### GridFS中的块被存储到专用的集合中,默认是fs.chunks
```
	bin/mongo 127.0.0.1:20001
	use dbsdb1
	db.fs.chunks.find();
	{ 
    "_id" : ObjectId("598fc1c11194fce1dbcd57a5"), 
    "files_id" : ObjectId("598fc1c11194fce1dbcd57a4"), 
    "n" : 0.0, 
    "data" : BinData(0, "xxxx")
	}

```


* 1:files_id:块所属文件的元信息

* 2:n:块在文件中的相对位置

* 3:data:块所包含的二进制数据
 


#### GridFS中每个文件的元信息被存储到单独的集合中,默认是fs.files
```
	db.fs.files.find()
	{ 
    "_id" : ObjectId("598fc1c11194fce1dbcd57a4"), 
    "chunkSize" : 261120.0, 
    "uploadDate" : ISODate("2017-08-13T03:04:33.114+0000"), 
    "length" : 2777.0, 
    "md5" : "fd12ab0bd883a9530331f35c9e04006c", 
    "filename" : "rsa3.txt"
}

```
* 1:_id:文件唯一的id,就是前面的files_id

* 2:length:文件包含的字节数

* 3:chunkSize:组成文件的每个快的大小,单位是字节,这个值默认是256k 

* 4:uploadDate:文件被上传到GridFS的日期 5:md5:文件内容的md5校验值





## 参考文章
https://docs.mongodb.com/manual/reference/program/mongofiles/

https://docs.mongodb.com/manual/core/gridfs/#gridfs-collections






















