## redis
### 什么是redis
	Redis 是使用c语言开发的一个高性能键值数据库，redis可以通过一些键值类型来存储数据。
#### 键值类型
##### String字符类型 	
##### map散列类型
##### list列表类型
##### set 集合类型
##### 有序集合类型

#### redis 的应用场景
* 缓存（数据查询、短连接、新闻内容、商品内容等等）。（最多使用）
* 分布式集群架构中的session分离。
* 聊天室的在线好友列表。
* 任务队列。（秒杀、抢购、12306等等）
* 应用排行榜。
* 网站访问统计。
* 数据过期处理（可以精确到毫秒）


#### redis 安装位置
	/usr/local/redis-3.2.5/src
#### 开启redis
	./redis-server redis.conf

#### 连接redis
	./redis-cli -h 127.0.0.1 -p 6379

## Redis 数据类型
### String
#### 赋值
##### 语法：set key value
	127.0.0.1:6379> set name lihao
	OK
------------------------------------------------------
#### 取值
##### 语法 ： get key 
	127.0.0.1:6379> get name
	"lihao"
------------------------------------------------------
#### 取值并赋值
##### 语法：getset key value
	127.0.0.1:6379> getset name huahua
	"lihao"
------------------------------------------------------
#### 设置/获取多个键值
##### 语法： mset key value [key value ....]
		  mget key [key ...]
	127.0.0.1:6379> mset key1 value1 key2 value2
	OK 
	127.0.0.1:6379> mget key1 key2
	1) "value1"
	2) "value2"

#### 删除 删除key 所有类型都可以删除
##### 语法： del key [key ...]
	127.0.0.1:6379> del key1 key2
	(integer) 2
------------------------------------------------------
#### 数值增减
* 递增数字
	当存储的字符串是整数时，Redis提供了一个实用的命令INCR，其作用是让当前键值递增，并返回递增后的值。
##### 语法 ： incr key
	127.0.0.1:6379> incr num
	(integer) 1
	127.0.0.1:6379> incr num
	(integer) 2
	127.0.0.1:6379> incr num
	(integer) 3	

* 递增指定的整数
#####  语法 ： incrby key increment	
	127.0.0.1:6379> incrby num 2
	(integer) 5

* 递减数值
##### 语法 ： decr key
	127.0.0.1:6379> decr num
	(integer) 4

* 减少指定的整数
##### 语法 ： decrby key decrement
	127.0.0.1:6379> decrby num 10
	(integer) -6
------------------------------------------------------
#### 向尾部追加值
	APPEND的作用是向键值的末尾追加value。如果键不存在则将该键的值设置为value，
	即相当于 SET key value。返回值是追加后字符串的总长度
##### append key value
	127.0.0.1:6379> append name "  hello"
	(integer) 13
------------------------------------------------------
#### 获取字符串长度
	STRLEN命令返回键值的长度，如果键不存在则返回0。
##### strlen key
	127.0.0.1:6379> strlen name
	(integer) 13

------------------------------------------------------

## hash 散列类型
#### redis hash介绍
	hash 叫散列类型，它提供了字段和字段值的映射。字段值只能是字符串类型，
	不支持散列类型，集合类型等其它类型

#### 赋值
	hset命令不区分插入和更新操作，当执行插入操作时hset命令返回1，当执行更新操作时返回0。

* 一次设置一个字段值	
##### 语法 hset key field value
	127.0.0.1:6379> hset user username lihao
	(integer) 1
------------------------------------------------------
* 一次设置多个一个字段值
##### 语法 hmset key filed value [field value ...]
	127.0.0.1:6379> hmset user age 26 email 9999@qq.com
	OK
* 当字段不存在时赋值，类似hset 区别在于如果字段存在，该命令不执行任何操作。
##### 语法 hsetnx key field value
	127.0.0.1:6379> hsetnx user age 48
	(integer) 0
------------------------------------------------------
#### 取值
* 	一次只能获取一个字段值
##### 语法 hget key field
	127.0.0.1:6379> hget user username
	"lihao"

* 	一次可以获取多个字段值
##### 语法 hmget key field [field]
	127.0.0.1:6379> hmget user username age
	1) "lihao"
	2) "26"

* 	获取所有字段值
##### 语法 hgetall key
	127.0.0.1:6379> hgetall user
	1) "username"
	2) "lihao"
	3) "age"
	4) "26"
	5) "email"
	6) "9999@qq.com"

------------------------------------------------------
#### 删除字段
	可以删除一个或多个字段，返回值是被删除的字段个数

##### 语法 hdel key  field [field ...]	
	127.0.0.1:6379> hdel user age email
	(integer) 2
------------------------------------------------------

#### 增加数字
##### 语法 hincrby key filed increment
	127.0.0.1:6379> hincrby user age 6
	(integer) 51

#### 只获取字段或字段值
##### 语法 hkeys key    hvals key
	127.0.0.1:6379> hkeys user
	1) "username"
	2) "age"
	3) "email"

	127.0.0.1:6379> hvals user
	1) "lihao"
	2) "51"
	3) "8888@qq.com"
------------------------------------------------------
#### 获取字段数量
##### 语法 hlen key
	127.0.0.1:6379> hlen user
	(integer) 3
------------------------------------------------------

### List
#### ArrayList 和 LinkedList的区别
* ArrayList 是使用数组来存储数据，特点：查询快，增删慢。
* LinkedList 是使用双向链表存储数据，特点：增删快，查询慢，但是查询链表
		两端的数据也很快，先进先出
* redis的list是采用链表来存储的，所以对于redis的list数据类型的操作，是操作list两端数据来操作的。
------------------------------------------------------

#### 向列表两端增加元素
* 向列表左边添加元素
##### 语法 lpush key value [value ...]
	127.0.0.1:6379> lpush list:2 1 2 3
	(integer) 3

* 向列表右边增加元素 
##### 语法 rpush key value [value ...]
	127.0.0.1:6379> rpush list:2 4 5 6
	(integer) 6
------------------------------------------------------
#### 查看列表
	lrange命令是列表类型最常用命令之一，获取列表的某一片段，将返回start,stop 之间所有元素（包含两端的元素），索引从0开始。索引可以是负数，“-1 代表最后边的一个元素” 

##### 语法 lrange key start stop	
	127.0.0.1:6379> lrange list:2 0 1
	1) "3"
	2) "2"

	127.0.0.1:6379> lrange list:2 0 -1
	1) "3"
	2) "2"
	3) "1"
	4) "4"
	5) "5"
	6) "6"
------------------------------------------------------
#### 从列表两端弹出元素
	lpop命令从列表左边弹出一个元素，会分两步完成：
	第一步是将列表左边的元素从列表中移除
	第二步是返回被移除的元素值

##### 语法 lpop key    rpop key
	127.0.0.1:6379> lpop list:2
	"1"
	127.0.0.1:6379> rpop list:2
	"6"

#### 获取列表中元素的个数
##### 语法 llen key
	127.0.0.1:6379> llen list:2
	(integer) 2
------------------------------------------------------
#### 删除列表中指定的值
lrem 命令会删除列表中前count 个值为value的元素，返回实际删除的元素个数。
格局count值的不同，该命令的执行方式会有所不同：
* 当 count>0 时，lrem 会从列表左边开始删除
* 当 count<0 时，lrem会从列表后边开始删除
* 当 count=0 时，lrem删除所有值为value的元素。

##### 语法 lrem key count vlaue
	127.0.0.1:6379> lrem list:2 2 8
	(integer) 2
	127.0.0.1:6379> lrem list:2 0 8
	(integer) 2

#### 获得指定索引的元素值
##### 语法 lindex key index
	127.0.0.1:6379> lindex list:1 2
	"b"

#### 设置指定索引的元素值
##### 语法 lset key index value
	127.0.0.1:6379> lset list:1 2 e
	OK

#### 只保留列表指定片段
##### 语法 ltrim key start stop
	127.0.0.1:6379> ltrim list:1 0 2
	OK
------------------------------------------------------
#### 向列表中插入元素
	该命令首先会在列表中从左到右查找值为pivot 的元素，然后根据第二个参数是before还是
	after来决定将value插入到该元素的前面还是后面。
##### 语法: linsert key before|after pivot value
	127.0.0.1:6379> linsert list:1 before d f
	(integer) 4	

------------------------------------------------------
## set
	集合类型
	集合类型：无序，不可重复
	列表类型：有序，可重复
------------------------------------------------------
#### 增加元素
##### 语法 sadd key member[member ...]
	127.0.0.1:6379> sadd set:1 a b c
	(integer) 3
------------------------------------------------------
#### 删除元素
##### srem key member[member...]
	127.0.0.1:6379> srem set:1 a
	(integer) 1
------------------------------------------------------
#### 获得集合中所有元素
##### smembers  key
	127.0.0.1:6379> smembers set:1
	1) "c"
	2) "b"

####  判断元素是否在集合中 0 不在 不在 1
##### sismember key member
	127.0.0.1:6379> sismember set:1 a
	(integer) 0
	127.0.0.1:6379> sismember set:1 b
	(integer) 1

------------------------------------------------------	
####  获得集合中元素的个数
##### 语法： scard key
	127.0.0.1:6379> scard set:1
	(integer) 2

####  从集合中弹出一个元素
	注意由于集合是无序的，所有spop命令会从集合中随机选择一个元素弹出。
##### 语法：spop key
	127.0.0.1:6379> spop set:1
	"a"
	127.0.0.1:6379> spop set:1
	"e"

------------------------------------------------------
## sortedset
* sorted又叫zset
* sortedset 是有序集合，可排序，但是唯一
* sortedset 和set 的不同之处，会给set的元素添加一个分数，然后通过这个分数进行排序
------------------------------------------------------
#### 增加元素
	向有序集合中加入一个元素和该元素的分数，如果该元素已经存在则会用新的分数替换原有的分数。
	返回值是新加入到集合中的元素个数，不包含之前已经存在的元素。
##### 语法: zadd key sore member[sore member]
	127.0.0.1:6379> zadd scorebard 80 zhangsan  89 lisi  30 wangwu
	(integer) 3
------------------------------------------------------
#### 获取元素的分数
##### 语法: zscore key member
	127.0.0.1:6379> zscore scorebard lisi
	"98"
------------------------------------------------------
#### 删除元素
##### 语法: zrem key member[member]
	127.0.0.1:6379> zrem scorebard lisi
	(integer) 1
#### 按照排名范围删除元素	
##### 语法: zremrangebyrank key start stop 从小到大开始删除
	127.0.0.1:6379> zremrangebyrank scorebard 0 1
	(integer) 2
#### 	按照分数范围删除元素
##### 语法: zremrangebyscore key min max
	127.0.0.1:6379> zremrangebyscore scorebard 20 40
	(integer) 1
------------------------------------------------------


#### 获得排名在某个范围的元素列表
* 按照元素分数从小到大的顺序返回索引从start到stop之间所有元素（包含两端的元素）
##### 语法: zrange key start top [withscores]
	127.0.0.1:6379> zrange scorebard 0 20 withscores
	1) "wangwu"
	2) "30"
	3) "huangdian"
	4) "78"
	5) "lihao"
	6) "78"
	7) "zhangsan"
	8) "80"
* 按照元素分数从大到小的顺序返回索引从start到stop之间所有元素（包含两端的元素）
##### 语法: zrevrange key start stop [withscores]
	127.0.0.1:6379> zrevrange scorebard 0 20 withscores
	1) "zhangsan"
	2) "80"
	3) "lihao"
	4) "78"
	5) "huangdian"
	6) "78"
	7) "wangwu"
	8) "30"
------------------------------------------------------
#### 获取元素的排名
* 从小到大
##### 语法: zrank key member
	127.0.0.1:6379> zrank scorebard lihao
	(integer) 2
* 从大到小
##### 语法: zrevrank key member
	127.0.0.1:6379> zrevrank scorebard zhangsan
	(integer) 0
------------------------------------------------------
#### 获得指定分数范围的元素
##### 语法：zrangebyscore key min max [withscores] [limit offset count] 
	127.0.0.1:6379> zrangebyscore scorebard 80 90 withscores
	 1) "zhangsan"
	 2) "80"
	 3) "adian"
	 4) "83"
	 5) "wangxiao"
	 6) "83"
	 7) "xiaohua"
	 8) "85"
	 9) "zhanghe"
	10) "88"
------------------------------------------------------
#### 增加某个元素的分数
	返回值是更改后的分数
##### 语法： zincrby key increment member
	127.0.0.1:6379> zscore scorebard xiaohua
	"85"
	127.0.0.1:6379> zincrby scorebard 5 xiaohua
	"90"	
#### 获得集合中元素的个数
##### 语法：zcard key
	127.0.0.1:6379> zcard scorebard
	(integer) 8

------------------------------------------------------
#### 获得指定分数范围内的元素个数
##### 语法： zcount key min max
	127.0.0.1:6379> zcount scorebard 70 90
	(integer) 7

------------------------------------------------------
#### 应用商品销售排行榜
	需求：根据商品销售量对商品进行排行显示
	思路：定义商品销售排行榜（sorted set集合），Key为items:sellsort，分数为商品销售量。

##### 写入商品销售量
* 商品编号1001的销量是9，商品编号1002的销量是10
	 ZADD items:sellsort 9 1001 10 1002	
* 商品编号1001的销量加1	 
	ZINCRBY items:sellsort 1 1001
* 商品销量前10名：
	ZRANGE items:sellsort 0 9 withscores
------------------------------------------------------

### keys  返回满足给定pattern 的所有key
	keys java*
------------------------------------------------------
### exists 确认一个key是否存在
	127.0.0.1:6379> exists lihao
	(integer) 0
	127.0.0.1:6379> exists test
	(integer) 1
------------------------------------------------------
### del 删除一个key
	del test
------------------------------------------------------
### rename 重命名key
	127.0.0.1:6379> rename test test_new
	OK
------------------------------------------------------
### type 返回值的类型
	127.0.0.1:6379> type scorebard
	zset
	127.0.0.1:6379> type list:2
	list	
------------------------------------------------------
### 设置key的生存时间
	redis 在实际使用过程中更多的用作缓存，然而缓存的数据一般都是需要设置生存时间的，
	即：到期后数据销毁.
#### 语法 expire key sconds 设置key的生存时间（单位：秒）key在多少秒后自动删除
#### ttl key  查看key 生存时间
#### pexpire key milliseconds 生存时间设置单位为：毫秒


## java 使用
#### 导入jar
	commons-pool2-2.3.jar
	jedis-2.7.0.jar

#### 单列链接
 	Jedis jedis = new Jedis("127.0.0.1", 6379);
    Set<String> keys = jedis.keys("*");
    Iterator<String> it = keys.iterator();
    while (it.hasNext()) {
        System.out.println(it.next());
    }
    jedis.close();


#### 连接池
	JedisPool pool = new JedisPool("127.0.0.1", 6379);
    Jedis jedis = pool.getResource();
    Set<String> keys = jedis.keys("java*");
    Iterator<String> it = keys.iterator();
    while (it.hasNext()) {
        System.out.println(it.next());
    }

    jedis.close();
    pool.close();























































































