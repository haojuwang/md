## 开启mongo服务
	/usr/local/opt/mongodb/bin/mogod
## 链接mongo
	mongo
## 查看所有的库
	show dbs	
## 选择库
	use 库名

## 查看所有的表
	 db.getCollectionNames();

## 查看表的所有数据
	db.表明.find()	 
## 创建MongoDB 创建数据库的语法格式如下
	use DATABASE_NAME
	如果你想查看所有数据库，可以使用 show dbs 命令创建的库并不存在要向表里面插入一条数据
	db.lihao.insert({"name":"李浩"})	

## 删除库
	db.dropDatabase()

## 插入文档
	MongoDB 使用 insert() 或 save() 方法向集合中插入文档
#### db.COLLECTION_NAME.insert(document)	
	db.lihao.insert({"title": 'MongoDB 教程',
	     "description": 'MongoDB 是一个 Nosql 数据库',
	     "by": '菜鸟教程',
	     "url": 'http://www.runoob.com',
	     "tags": ['mongodb', 'database', 'NoSQL'],
	     "likes": 100
	 })

## 更新文档
#### update() 方法
	db.collection.update(
	   <query>,
	   <update>,
	   {
	     upsert: <boolean>,
	     multi: <boolean>,
	     writeConcern: <document>
	   }
	)
参数说明：
* query : update的查询条件，类似sql update查询内where后面的。
* update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
* upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
* multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
* writeConcern :可选，抛出异常的级别。	
###### 实例
	db.lihao.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}})

#### save() 方法
	db.collection.save(
	   <document>,
	   {
	     writeConcern: <document>
	   }
	)
###### 实例
	db.lihao.save({"title":"李三"})

#### 更多实例
##### 只更新第一条记录：
	db.col.update( { "count" : { $gt : 1 } } , { $set : { "test2" : "OK"} } );
##### 全部更新：
	db.col.update( { "count" : { $gt : 3 } } , { $set : { "test2" : "OK"} },false,true );
##### 只添加第一条：
	db.col.update( { "count" : { $gt : 4 } } , { $set : { "test5" : "OK"} },true,false );
##### 全部添加加进去:
	db.col.update( { "count" : { $gt : 5 } } , { $set : { "test5" : "OK"} },true,true );
##### 全部更新：
	db.col.update( { "count" : { $gt : 15 } } , { $inc : { "count" : 1} },false,true );
##### 只更新第一条记录：
	db.col.update( { "count" : { $gt : 10 } } , { $inc : { "count" : 1} },false,false );

## remove() 删除文档
	db.collection.remove(
	   <query>,
	   {
	     justOne: <boolean>,
	     writeConcern: <document>
	   }
	)

	db.lihao.remove({"title":"李三"})	

### 删除所有数据
	db.col.remove({})

## MongoDB 查询文档
#### 语法格式
	db.COLLECTION_NAME.find()
	db.lihao.find({"title":"MongoDB"})

#### 条件

操作|格式|范例|rdbms中类似语句
:----------------|:----------------------:|----------------------:|---------------:
等于|{<`key`>:<`value>}           | db.col.find({"by":"菜鸟教程"})   |where by = '菜鸟教程
小于|{<`key>:{$lt:<`value>}}      | db.col.find({"likes":{$lt:50}})|where likes < 50
小于或等于|{<`key>:{$lte:<`value>}}| db.col.find({"likes":{$lte:50}})|where likes <= 50
大于|{<`key>:{$gt:<`value>}}      | db.col.find({"likes":{$gt:50}}) |where likes > 50
大于或等于|{<`key>:{$gte:<`value>}}| db.col.find({"likes":{$gte:50}}) |where likes >= 50
不等于|{<`key>:{$ne:<`value>}}    | db.col.find({"likes":{$ne:50}}) | where likes != 50
模糊|{<`key>:{$regex:<`value>}}   | db.lihao.find({"title":{$regex: 'Mon', $options:'i'}})|where title like "%Mon%"

## MongoDB Limit与Skip方法
### MongoDB Limit() 方法
	如果你需要在MongoDB中读取指定数量的数据记录，可以使用MongoDB的Limit方法，limit()方法接受一个数字参数，该参数指定从MongoDB中读取的记录条数.
#### 语法
	db.COLLECTION_NAME.find().limit(NUMBER)
	db.test.find().limit(2)

### MongoDB skip() 方法
	我们除了可以使用limit()方法来读取指定数量的数据外，还可以使用skip()方法来跳过指定数量的数据，skip方法同样接受一个数字参数作为跳过的记录条数。
#### 语法
	db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)	
	db.test.find().limit(2).skip(2)

## MongoDB 排序
### MongoDB sort()方法
	在MongoDB中使用使用sort()方法对数据进行排序，
	sort()方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而-1是用于降序排列。
#### 语法
	db.COLLECTION_NAME.find().sort({KEY:1})	

---------------------------------------------------------
## java 操作mongodb
	添加 mongo-java-driver-3.3.0.jar

### 连接mongo 	
	String uriStr = "mongodb://127.0.0.1:27017/";
    MongoClientURI uri = new MongoClientURI(uriStr);
    MongoClient mongoClient = new MongoClient(uri);

### 创建数据库
	public void createDatabase(){
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoClient = new MongoClient(uri);
        // 连接到数据库 如果不存在会创建数据库
        MongoDatabase mongoDatabase = mongoClient.getDatabase("mycol");
        //不创建表数据库创建不成功
        mongoDatabase.createCollection("test");
    }

### 查看所有的数据库
	public void showDbs() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoclient = new MongoClient(uri);

        //查看所有的库
        List<String> database = mongoclient.getDatabaseNames();
        System.out.println(database.size());
        System.out.println(database);

        //打开指定库
        MongoDatabase db = mongoclient.getDatabase("testdb");
        System.out.println(db.getName());

        mongoclient.close();

    }

### 查看库里面的表
	public void showTables() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoClient = new MongoClient(uri);

        MongoDatabase testDb = mongoClient.getDatabase("testdb");
        MongoIterable<String> tables = testDb.listCollectionNames();
        System.out.println(tables);
        MongoCursor<String> it = tables.iterator();
        while (it.hasNext()) {
            System.out.println(it.next());
        }
    }

### insert 插入数据插入一条
	public void insert() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoClient = new MongoClient(uri);
        MongoDatabase database = mongoClient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");

        MongoBean bean =  new MongoBean("huangdiandian",25,2);
        Gson gson = new Gson();
        Document document = Document.parse(gson.toJson(bean));
        account.insertOne(document);
    }

### insertMany 插入多个数据
	public void inserMany() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoClient = new MongoClient(uri);
        MongoDatabase database = mongoClient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");
        ArrayList<Document> listMongo = new ArrayList<Document>();
        Gson gson = new Gson();

        for(int i=1;i<10;i++) {
            MongoBean bean =  new MongoBean("huangdiandian"+i,25+i,2);
            listMongo.add( Document.parse(gson.toJson(bean)));
        }
        account.insertMany(listMongo);

    }

### update 更新数据
    public void update() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoClient = new MongoClient(uri);
        MongoDatabase database = mongoClient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");
        
        //提交 相当于 where
        BasicDBObject where = new BasicDBObject();
        where.append("name","li");

        //设置 值  set
        Document set = new Document();
        set.append("name","乐约");
        set.append("age",23);
        //修改多个可以用updateMany
        account.updateOne(where,new Document("$set",set));
        mongoClient.close();
    }

### delete 删除数据
	public void delete() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoclient = new MongoClient(uri);
        MongoDatabase database = mongoclient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");
        
        //where 条件
        Document document =  new Document();
        document.append("name","乐约");
        document.append("age",4);
        //删除多个用deleteMany()
        DeleteResult del = account.deleteOne(document);
        System.out.println("删除了: "+del.getDeletedCount());
        
        mongoclient.close();

    }    

### find 查询显示指定字段
	public void findWidthField() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoClient = new MongoClient(uri);
        MongoDatabase database = mongoClient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");
        
        //显示指定字段
        Document fieldBeson = new Document();
        fieldBeson.put("name", true);
        fieldBeson.put("age", true);
        fieldBeson.put("_id", false);

        FindIterable<Document> documents = account.find().projection(fieldBeson);
        for (Document document : documents) {
            System.out.println(document);
        }

        mongoClient.close();

    }

### find 按照条件查询
	 public void findWidthCondition() {
        QueryOperators.
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoClient = new MongoClient(uri);
        MongoDatabase database = mongoClient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");
        // age 大于等于 22
        BasicDBObject queryBeson = new BasicDBObject();
        // age >= 22
        queryBeson.append("age",new BasicDBObject(QueryOperators.GTE,22));
        queryBeson.append(QueryOperators.GTE,new BasicDBObject("age",22));
        // age = 22  or age = 20
        queryBeson.append(QueryOperators.OR,new BasicDBObject[] { new 		BasicDBObject("age", 22),
                new BasicDBObject("age", 20) } );

        //name=lihao and age=1
        queryBeson.append(QueryOperators.AND,new BasicDBObject[]{
                new BasicDBObject("name","lisan"),
                new BasicDBObject("sex",1)

        });
        //name = li
        queryBeson.append("name",new BasicDBObject(QueryOperators.IN,new String[]{"li"}));


        findCondition(account, queryBeson);


        mongoClient.close();

    }

    private void findCondition(MongoCollection<Document> account, BasicDBObject queryBeson) {
        Document fieldBeson = new Document();
        fieldBeson.put("name", true);
        fieldBeson.put("age", true);
        fieldBeson.put("sex", true);
        fieldBeson.put("_id", false);

        FindIterable<Document> documents = account.find(queryBeson).projection(fieldBeson);

        for (Document document : documents) {
            System.out.println(document);
        }
    }


### limit 显示指定行数的数据
    public void limit() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoclient = new MongoClient(uri);
        MongoDatabase database = mongoclient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");

        FindIterable<Document> documents = account.find().limit(2);
        for(Document document : documents) {
            System.out.println(document.toJson());
        }
    }

### skip 来跳过指定数量的数据
	 public void skip() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoclient = new MongoClient(uri);
        MongoDatabase database = mongoclient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");

        FindIterable<Document> documents = account.find().limit(2).skip(2);
        for(Document document : documents) {
            System.out.println(document.toJson());
        }
    }    

### sort 排序
	public void sort() {
        String uriStr = "mongodb://127.0.0.1:27017/";
        MongoClientURI uri = new MongoClientURI(uriStr);
        MongoClient mongoclient = new MongoClient(uri);
        MongoDatabase database = mongoclient.getDatabase("testdb");
        MongoCollection<Document> account = database.getCollection("account");

        //order by 1 正序 -1 倒叙
        Document document = new Document().append("age",-1);

        FindIterable<Document> documents = account.find().sort(document);
        for(Document doc : documents) {
            System.out.println(doc.toJson());
        }

    }


----------------------------------------------------------------
2016.12.15





















