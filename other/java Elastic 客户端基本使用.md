

## java Elastic 客户端基本使用

#### 引入jar

```java
   compile 'org.elasticsearch:elasticsearch:5.5.0'

   compile 'org.elasticsearch.client:transport:5.5.0
```

### client基本使用

#### 得到client

```java
Settings settings = Settings.builder().put("cluster.name", "lw-6-test").build();
TransportClient client = new PreBuiltTransportClient(settings);
client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.10.10.6"), 9300));
```

#### 关闭资源

```
client.close();
```



* 搜索关键字全部要小写。

#### get得到指定index type id的数据

```java
  public static void prepareGet(TransportClient client) throws Exception {
        GetResponse response = client.prepareGet("mytest", "test", "p1").get();
        System.out.println(response);
    }
    
 output:
 
{"_index":"mytest","_type":"test","_id":"p1","_version":1,"found":true,"_source":{"name":"mac Book 笔记本",
"price":1233,
"description":"这是笔记本",
"cats":["3c","computer"]

}
}
    
    
```

#### insert添加数据

```java
  public static void insert(TransportClient client) throws Exception {
        Map<String,Object> resource = new HashMap<>();
        resource.put("name","mac Note");
        resource.put("price",8877);
        resource.put("description","mac Note 新款");
        IndexRequestBuilder index = client.prepareIndex("mytest", "test");

        IndexResponse insertResponse = index.setSource(resource).execute().get();

        System.out.println(insertResponse);
    }
    
    
output:
	IndexResponse[index=mytest,type=test,id=AV8CZmTgGnilLCUrybiV,version=1,result=created,shards={"total":2,"successful":1,"failed":0}]
```



#### delete删除数据

```java
  public static void delete(TransportClient client) throws Exception{
        BulkByScrollResponse response = DeleteByQueryAction.INSTANCE.newRequestBuilder(client)
                .filter(QueryBuilders.matchQuery("name", "mac")) //搜索
                .source("mytest") //index
                .get();


       long deleted = response.getDeleted();
       System.out.println("删除个数: "+deleted);

    }
       
```



#### update 修改数据

```java
 public static void update(TransportClient client) throws Exception{

        Map<String,Object> data = new HashMap<>();
        data.put("name","new mac node");

        UpdateRequest updateRequest =  new UpdateRequest();
        updateRequest.index("mytest");
        updateRequest.type("test");
        updateRequest.id("AV8CfcSLGnilLCUryoEl");

        updateRequest.doc(data);

        UpdateResponse response = client.update(updateRequest).get();
        System.out.println(response);

    }
    
output:
UpdateResponse[index=mytest,type=test,id=AV8CfcSLGnilLCUryoEl,version=2,result=updated,shards=ShardInfo{total=2, successful=1, failures=[]}]
```



#### MultiGet查询多个index

```java
    public static void multiIndex(TransportClient client) throws Exception {
        MultiGetResponse multiGetItemResponses = client.prepareMultiGet()
                .add("mytest","test","AV8CfcSLGnilLCUryoEl") //多个index
                .add("instestdb_business_log-2017.09","instestdb_business_log","AV7KHPtGDF9uyeK_lXln") //多个index
                .get();

        for(MultiGetItemResponse itemResponses : multiGetItemResponses) {
            GetResponse response = itemResponses.getResponse();
            if(response.isExists()) { 
                String json = response.getSourceAsString(); //获取到_source field
                System.out.println(json);
            }

        }
        
    }
```



#### Bulk API 一次请求多个添加和删除

```java
 public static void BulkRequest(TransportClient client) throws Exception {
        BulkRequestBuilder bulkRequest = client.prepareBulk();

        IndexRequestBuilder index1 = client.prepareIndex("mytest", "test");
        IndexRequestBuilder index2 = client.prepareIndex("mytest", "test");

        Map<String,Object> resource = new HashMap<>();
        resource.put("name","华硕");
        resource.put("price",5577);
        resource.put("description","华硕z460");

        index1.setSource(resource);

        Map<String,Object> resource1 = new HashMap<>();
        resource1.put("name","小米2");
        resource1.put("price",4577);
        resource1.put("description","新机超薄");

        index1.setSource(resource);
        index2.setSource(resource1);

        bulkRequest.add(index1);
        bulkRequest.add(index2);


        BulkResponse bulkResponse = bulkRequest.get();


        if(bulkResponse.hasFailures()) {
            System.out.println(bulkResponse.buildFailureMessage());
        }

        bulkResponse.forEach(response ->{
            System.out.println(response.getId());
        });

    }
```



## query dsl 使用

```java
import static org.elasticsearch.index.query.QueryBuilders.*;

 Settings settings = Settings.builder()
                .put("cluster.name", "lw-6-test").build();
        TransportClient client = new PreBuiltTransportClient(settings);
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.10.10.6"), 9300));
		
        allquery(client); //具体的query dsl查询

        client.close();

```

####  Match All Query 查询所有的数据

```java

    public static void allquery(TransportClient client) throws Exception{
        QueryBuilder qb = matchAllQuery();
        SearchResponse response = client.prepareSearch("mytest").setTypes("test").setSize(3).setQuery(qb).get();

        System.out.println("length: "+response.getHits().getHits().length );
        if(response.getHits().getTotalHits() != 0) {
            for (SearchHit hit : response.getHits().getHits()) {

                System.out.println(hit.getSourceAsString());
            }
        }
    }
```

#### Match Query 查询单一条件的数据

```java
  public static void myMatchQuery(TransportClient client) throws Exception {
        QueryBuilder qb = matchQuery("name","mac");
        
        SearchResponse response = client.prepareSearch("mytest").setTypes("test").setQuery(qb).get();
        System.out.println("length: "+response.getHits().getHits().length );
        if(response.getHits().getTotalHits() != 0) {
            for (SearchHit hit : response.getHits().getHits()) {
                System.out.println(hit.getScore()+" --> "+hit.getSourceAsString());
            }

        }
    }
```



MultiMatchQuery 在多个字段中查询一个关键字

```
  QueryBuilder qb = multiMatchQuery("mac","description","name"); //mac是要搜索的词 description，name 都是字段
```



#### Common Terms Query 搜索term

```java
 public static void myCommonTermsQuery(TransportClient client ) throws Exception{
        QueryBuilder qb = commonTermsQuery("description","mac");
        print(qb,client);
    }
```



#### Simple Query String Query 简单字符串查询可以使用正则

```java
public static void mySimpleQueryString(TransportClient client ) {
        QueryBuilder qb = queryStringQuery("mac*^2").field("name");
        print(qb,client);
    }
```



#### term 搜索关键词一个

```java
 public static void myTermQuery(TransportClient client) {
        QueryBuilder qb = termQuery("name","mac2");
        print(qb,client);
    }
```



#### terms 搜索关键词多个

```
   QueryBuilder qb = termsQuery("name_str","小米","戴尔");
   print2(qb,client);
```



#### range query 范围搜索

```java
  public static void myRangeQuery(TransportClient client){
        QueryBuilder qb = rangeQuery("price").from(3399)
                .to(6399)
                .includeLower(true)
                .includeUpper(false);

        print2(qb,client);
        
    }
    
    QueryBuilder qb = rangeQuery("price").gte(3399).lt(6399);
    
```



#### Exists Query 查找字段是否存在 存在则返回所有的数据，不存在返回0

```java
    public static void myExistsQuery(TransportClient client) {
        QueryBuilder qb = existsQuery("name_str");
        print2(qb,client);
    }
```



#### Wildcard Query 通配符查询

```
QueryBuilder qb = wildcardQuery("user", "k?mc*");
```

​		

#### Regexp Query支持正则表达式的查询

```
QueryBuilder qb = regexpQuery("name.first", "s.*y");
```

#### Fuzzy Query 模糊查询

```java
QueryBuilder qb = fuzzyQuery(
    "name",     
    "kimzhy"    
);
```



#### ids Query  根据id 查询

```java
QueryBuilder qb = idsQuery("my_type", "type2")
    .addIds("1", "4", "100");

QueryBuilder qb = idsQuery() 
    .addIds("AV8HhVC8FiG-4m4G2rYp","AV8HhVB6FiG-4m4G2rYm");
```

​			

## 复合查询

#### Contant Score Query 指定score		

```java
  QueryBuilder qb = constantScoreQuery(matchQuery("name_str", "联想")).boost(3.0f);
```

#### Bool Query  must mustNot should 查询

```
所有的 must 子句必须匹配, 并且所有的 must_not 子句必须不匹配, 但是多少 should 子句应
该匹配呢? 默认的,不需要匹配任何 should 子句,一种情况例外:如果没有must子句,就必须至少匹 配一个should子句。

```



```java
  public static void myBoolQuery(TransportClient client) {
        QueryBuilder qb = boolQuery().must(termQuery("name_str","小米"))
                .filter(matchQuery("price",3599))
                .filter(matchQuery("description","lihao"));
        print2(qb, client);
    }
    
    QueryBuilder qb = boolQuery()
    .must(termQuery("content", "test1"))    
    .must(termQuery("content", "test4"))    
    .mustNot(termQuery("content", "test2")) 
    .should(termQuery("content", "test3"))  
    .filter(termQuery("content", "test5"));
    
```

​		

#### indices query查询多个index

```
用来查询多个index,对于指定内的index,执行指定的内部query;对于指定外的index,执行
no_match_query设定的条件
```

```java
private static void print(QueryBuilder qb, TransportClient client) {
        SearchResponse response = client.prepareSearch("mytest").setTypes("test").setQuery(qb).get();
        System.out.println("length: " + response.getHits().getHits().length);
        if (response.getHits().getTotalHits() != 0) {
            for (SearchHit hit : response.getHits().getHits()) {
                System.out.println(hit.getScore() + " --> " + hit.getSourceAsString());
            }

        }
    }

    private static void print2(QueryBuilder qb, TransportClient client) {
        SearchResponse response = client.prepareSearch("mytest_1").setTypes("test").setQuery(qb).get();
        System.out.println("length: " + response.getHits().getHits().length);
        if (response.getHits().getTotalHits() != 0) {
            for (SearchHit hit : response.getHits().getHits()) {
                System.out.println(hit.getScore() + " --> " + hit.getSourceAsString());
            }

        }
    }
```



## scroll分页			

```java
 public static void main(String ...arg) throws Exception {

        //链接服务器
        Settings  settings = Settings.builder()
                .put("cluster.name","lw-6-test").build();

        TransportClient client = new PreBuiltTransportClient(settings);
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.10.10.6"), 9300));


        //设置搜索条件
        QueryBuilder qb = termQuery("name_str","笔记本");


        // 按照price 降序  每次查询2条  第一次不需要设置sroll  scrollid
        SearchResponse scrollResp = client.prepareSearch("mytest_1").setTypes("test")
                .addSort("price", SortOrder.DESC)
                .setScroll(new TimeValue(30000))
                .setQuery(qb)
                .setSize(2).get();

        System.out.println("length: " + scrollResp.getHits().getHits().length);
        int count = 1;
        do{
            System.out.println("第 " +count+ " 次");
            System.out.println();
            for (SearchHit hit : scrollResp.getHits().getHits()){
                System.out.println(hit.getScore() + " --> " +hit.getSourceAsString());
            }


            System.out.println("scrollid:  "+scrollResp.getScrollId());

            //设置sroll id
            scrollResp =client.prepareSearchScroll(scrollResp.getScrollId()).setScroll(new TimeValue(60000)).execute().actionGet();
            System.out.println();

            count++;

        } while (scrollResp.getHits().getHits().length !=0);


        client.close();

    }
```

​		

## prepareMultiSearch多个条件查询

```java
public class MultiSearchDemo {

    public static void main(String ...arg) throws Exception{
        Settings settings = Settings.builder().put("cluster.name", "lw-6-test").build();

        TransportClient client = new PreBuiltTransportClient(settings);
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.10.10.6"), 9300));


       QueryBuilder query1 = termQuery("name_str","小米");
       QueryBuilder query2 = termQuery("name_str","戴尔");

        SearchRequestBuilder srb1 = client.prepareSearch("mytest_1").setTypes("test").setQuery(query1);
        SearchRequestBuilder srb2 = client.prepareSearch("mytest_1").setTypes("test").setQuery(query2);

        MultiSearchResponse sr = client.prepareMultiSearch().add(srb1).add(srb2).get();

        long nbHits =0;
        for(MultiSearchResponse.Item item : sr.getResponses()) {
            SearchResponse response = item.getResponse();
            nbHits += response.getHits().getTotalHits();

            if(response.getHits().getHits().length >0) {

                for(SearchHit hit : response.getHits().getHits()) {
                    System.out.println(hit.getScore()+"  ----->  "+hit.getSourceAsString());
                }

            }
            System.out.println("-------------------------");
        }

        System.out.println(nbHits);

        client.close();

    }
}

```



## 		聚合

#### 概述

ES 的聚合框架提供对查询得到的数据进行分组和汇总统计，以提供复杂的统计分析功能。

ES支持在一次聚合查询中，可以同时得到聚合的具体结果并再次进行聚合，也就是聚合是可以嵌套的。

这非常有用,你可以通过一次操作得到多次聚合的结果,从而避免多次请求,减少网络和服务器的负担。





#### 聚合的类型

1：Bucketing（桶）聚合：划分不同的“桶”，将数据分配到不同的“桶”	里，然后再进行聚合，非常类似sql 中的group 语句的含义。

2：Metric(指标)聚合：指标聚合主要针对number类型的数据，在一组文档中，保持对要聚合的指标的跟踪和计算，需要ES做比较多的计算工作。

3：Pipeline(管道)聚合：用来聚合其它聚合输出的结果以及相关指标



#### 聚合的基本语法

```
"aggregations" : {    					//定义聚合对象,也可用 "aggs"
    "<aggregation_name>" : {			//聚合的名称,用户自定义	
        "<aggregation_type>" : {         //聚合类型,比如 "histogram" "avg"
            <aggregation_body>
        }
        [,"meta" : {  [<meta_data_body>] } ]?
        [,"aggregations" : { [<sub_aggregation>]+ } ]?
    }
    [,"<aggregation_name_2>" : { ... } ]*  ////定义额外的多个平级聚合,只有Bucketing类型才有意义
}
```

​		

```
GET mytest_1/test/_search
{
  "aggs" : {
        "avg_price" : { "avg" : { "field" : "price" } }
    }
}

output
{ "aggregations": {
    "avg_price": {
      "value": 4954.555555555556
    }
  }

```

​				
​		

### Metric 使用



#### 求平均值

```
   public static void main(String ...arg) throws Exception {

        Settings settings = Settings.builder().put("cluster.name", "lw-6-test").build();

        TransportClient client = new PreBuiltTransportClient(settings);
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.10.10.6"),9300));


        avg(client);



        client.close();


    }
    
     private static void avg(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test");

        SearchResponse sr = search.addAggregation(AggregationBuilders.avg("avg_price").field("price")).execute().actionGet();

        Avg result = sr.getAggregations().get("avg_price");
        System.out.println(result.getValue());
    }
    
    
    POST mytest_1/test/_search?size=0
{
  "aggs" : {
        
        "avg_price" : { 
          
          "avg" : { "field" : "price" } 
          
        }
    }
}
```

```
POST mytest_1/test/_search?size=0
{
   "aggs" : {
        "all_cats" : {
          "terms" : { "field" : "tag.keyword" },
              "aggs" : {
                  "avg_price" : {
                      "avg" : { "field" : "price" }
                  }

                
              }
        }
     
   }
}


  "aggregations": {
    "all_cats": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "笔记本",
          "doc_count": 8,
          "avg_price": {
            "value": 5124
          }
        },
        {
          "key": "联想",
          "doc_count": 4,
          "avg_price": {
            "value": 5649
          }
        },
        {
          "key": "小米",
          "doc_count": 2,
          "avg_price": {
            "value": 4399
          }
        },
        {
          "key": "惠普",
          "doc_count": 1,
          "avg_price": {
            "value": 2399
          }
        },
        {
          "key": "戴尔",
          "doc_count": 1,
          "avg_price": {
            "value": 7199
          }
        }
      ]
    }
  }
```

#### 分类求取平均值

```java
PUT mytest_1/_mapping/test
{
  "properties": {
    "tag": { 
      "type":     "text",
      "fielddata": true
    }
  }
}

    private static void avg1(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setSize(0).setTypes("test");

        TermsAggregationBuilder tag = AggregationBuilders.terms("tags").field("tag.keyword");
        AvgAggregationBuilder price = AggregationBuilders.avg("avg_price").field("price");
        tag.subAggregation(price);

        SearchResponse sr = search.addAggregation(tag).execute().actionGet();

        System.out.println(sr);
    }


```



#### Cardinality  去除重复数据

用来对单个数据进行汇总,计算不重复的值的数量。

```java
    public static void main(String... arg) throws Exception {

        Settings settings = Settings.builder().put("cluster.name", "lw-6-test").build();

        TransportClient client = new PreBuiltTransportClient(settings);
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.10.10.6"), 9300));


        cardinality(client);


        client.close();


    }
    
  private static void cardinality(TransportClient client) {

        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test");
        SearchResponse sr = search.addAggregation(AggregationBuilders.cardinality("type_count").field("price")).execute().actionGet();

        Cardinality result = sr.getAggregations().get("type_count");

        System.out.println("type_count: "+result.getValue());

    }
    
    
POST mytest_1/test/_search?size=0
{
    "aggs" : {
        "type_count" : {
            "cardinality" : {
                "field" : "price"
            }
        }
    }
}

out:
{
  "took": 7,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 8,
    "max_score": 0,
    "hits": []
  },
  "aggregations": {
    "type_count": {
      "value": 6
    }
  }
}
```

​			
​		

#### Stats 聚合操作 count min max avg	 sum 

```java
    private static void stats(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test");
        SearchResponse sr =  search.addAggregation(AggregationBuilders.stats("price_stats").field("price")).execute().actionGet();

       Stats stats = sr.getAggregations().get("price_stats");
        System.out.println(stats.getAvgAsString());
        System.out.println(stats.getMaxAsString());
        System.out.println(stats.getMinAsString());
        System.out.println(stats.getSumAsString());

    }
    
    
POST mytest_1/test/_search?size=0
{
    "aggs" : {
        "price_stats" : { "extended_stats" : { "field" : "price" } }
    }
}

out:
 "aggregations": {
    "price_stats": {
      "count": 8,
      "min": 2399,
      "max": 7199,
      "avg": 5124,
      "sum": 40992,
      "sum_of_squares": 231958008,
      "variance": 2739375,
      "std_deviation": 1655.1057368035433,
      "std_deviation_bounds": {
        "upper": 8434.211473607087,
        "lower": 1813.7885263929134
      }
    }
  }
```

​		

#### Percentiles 百分比 查看一个百分比对应的值

这是一个多值的指标聚集,用来计算聚合文档中,在某个百分比或某个区间,所对应的观测值,

1:缺省的percentile的区间是[ 1, 5, 25, 50, 75, 95, 99 ]。


2:观测值通常都是近似的,有很多不同的算法来计算。			
​		
​	例如:第九十五个百分值是大于所观察到的值的95%的值。

```
POST mytest_1/test/_search?size=0
{
    "aggs" : {
        "price_percent" : {
            "percentiles" : {
                "field" : "price" 
            }
        }
    }
}

POST mytest_1/test/_search?size=0
{
    "aggs" : {
        "price_percent" : {
            "percentiles" : {
                "field" : "price" ,
                "percents" : [0.1,50,95, 99, 100]  //自定义百分比区间
            }
        }
    }
}


  "aggregations": {
    "price_percent": {
      "values": {
        "1.0": 2468.9999999999995,
        "5.0": 2749,
        "25.0": 3549,
        "50.0": 5799,
        "75.0": 6399,
        "95.0": 6918.999999999999,
        "99.0": 7143
      }
```



```java
 private static void percentile(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test");
        SearchResponse sr =  search.addAggregation(AggregationBuilders.percentiles("price_percent").field("price")).execute().actionGet();

       Percentiles percentile = sr.getAggregations().get("price_percent");

       System.out.println(percentile.percentileAsString(80));
    }

	//自定义百分比区间
    private static void percentile2(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test");
        SearchResponse sr =  search.addAggregation(AggregationBuilders.percentiles("price_percent").percentiles(0.1,50,95, 99, 100).field("price")).execute().actionGet();

        System.out.println(sr);
        Percentiles percentile = sr.getAggregations().get("price_percent");

        System.out.println(percentile.percentileAsString(80));
    }
```

​			

#### Value Count 计算聚合值的数量

```
POST mytest_1/test/_search?size=0
{
"aggs" : {
        "types_count" : { "value_count" : { "field" : "price" } }
    }
    
}  

out:
"aggregations": {
    "types_count": {
      "value": 8
    }
```

```java
 private static void valueCount(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test");
        SearchResponse sr =search.addAggregation(AggregationBuilders.count("value_count").field("price")).execute().actionGet();
       ValueCount valueCount = sr.getAggregations().get("value_count");
       System.out.println(valueCount.getValue());

    }
```

​		

#### TOP hits		

用来取符合条件的前n条数据。 包含的选项有:from、size、sort。

```java
    private static void topHits(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test");

        TopHitsAggregationBuilder addtion = AggregationBuilders.topHits("top_price_hits").sort("price", SortOrder.DESC).fieldDataField("price")
                .size(5);

        SearchResponse sr =search.addAggregation(addtion).execute().actionGet();
       TopHits topHits = sr.getAggregations().get("top_price_hits");
       System.out.println();
        SearchHit[] hits = topHits.getHits().internalHits();
        for(SearchHit searchHit : hits) {
            System.out.println(searchHit.getSourceAsString());

        }

    }
```

​			
​		

## bucket 使用

#### Histogram

条形图聚合，根据文档中的谋改革字段来分组。一个文档属于某个通，计算过程大致如下：

```
rem = value % interval
if (rem < 0) {
    rem += interval
}
bucket_key = value - rem
```

1:可配置的参数:
(1)field:字段,必须为数值类型

(2)interval:分桶间距 (3)min_doc_count:最少文档数,桶过滤,只有不少于这么多文档的桶才会返回 

(4)extended_bounds:范围扩展

(5)order:对桶排序,如果 histogram 聚合有一个权值聚合类型的“直接”子聚合,那么排序可以使用 子聚合中的结果

(6)offset:桶边界位移,默认从0开始 (7)keyed:hash结构返回,默认以数组形式返回每一个桶 (8)missing:配置缺省默认值

```
POST mytest_1/test/_search?size=0
{
    "aggs" : {
        "prices" : {
            "histogram" : {
                "field" : "price",
                "interval" : 2000
            }
        }
    }
}

out:
 "aggregations": {
    "prices": {
      "buckets": [
        {
          "key": 2000,
          "doc_count": 3
        },
        {
          "key": 4000,
          "doc_count": 1
        },
        {
          "key": 6000,
          "doc_count": 4
        }
      ]
    }

```

```java
  private static void histogram(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test").setSize(0);
        HistogramAggregationBuilder addtion = AggregationBuilders.histogram("prices").interval(2000).field("price");

        SearchResponse sr = search.addAggregation(addtion).execute().actionGet();

       Histogram histogram = sr.getAggregations().get("prices");
       histogram.getBuckets().forEach(bucket->{
           System.out.println(bucket.getKeyAsString()+" ---->  "+bucket.getDocCount());
       });
    }
```



#### Range

范围聚合，是对某个字段的值，按照设定的范围进行分组。

```
POST mytest_1/test/_search?size=0
{
    "aggs" : {
        "price_ranges" : {
            "range" : {
                "field" : "price",
                "ranges" : [
                    { "to" : 3000 },
                    { "from" : 3000, "to" : 5000 },
                    { "from" : 5000 }
                ]
            }
        }
    }
}

out:
"aggregations": {
    "price_ranges": {
      "buckets": [
        {
          "key": "*-3000.0",
          "to": 3000,
          "doc_count": 1
        },
        {
          "key": "3000.0-5000.0",
          "from": 3000,
          "to": 5000,
          "doc_count": 2
        },
        {
          "key": "5000.0-*",
          "from": 5000,
          "doc_count": 5
        }
      ]
    }
```





```java
   private static void range(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test").setSize(0);
        AggregationBuilder addtion = AggregationBuilders.range("price_ranges").field("price")
                .addUnboundedTo(3000)
                .addRange(3000,5000)
                .addUnboundedFrom(5000);

        SearchResponse sr = search.addAggregation(addtion).execute().actionGet();

       Range histogram = sr.getAggregations().get("price_ranges");
       histogram.getBuckets().forEach(bucket->{
           String key = bucket.getKeyAsString();
           String from = bucket.getFromAsString();
           String to = bucket.getToAsString();
           long count = bucket.getDocCount();
           System.out.println("key : "+key+"\t form: "+from+"\t to:"+to+"\t count:"+count);
       });
    }
```



#### 	Date Range



#### Ip Range



#### Terms

词元聚合，以指定的字段内的每一个不重复的term来分组，并计算每个组内文档的个数。

```
POST mytest_1/test/_search?size=0
{
   "aggs" : {
        "all_cats" : {
          "terms" : { "field" : "tag.keyword" }
     
        }
     
   }
}
```



#### Filters 过滤

多过滤聚合，用多个过滤条件，来对当前文档进行过滤的聚合，每个过滤都包含所有满足它的文档，

多个bucket中可能重复。

```
POST mytest_1/test/_search?size=0
{
  "size": 0,
  "aggs" : {
    "messages" : {
      "filters" : {
        "filters" : {
          "filter1" :   { "match" : { "tag" : "小米"   }},
          "filter2" : { "match" : { "tag" : "戴尔" }}
        }
      }
    }
  }
}

out:
"aggregations": {
    "messages": {
      "buckets": {
        "filter1": {
          "doc_count": 2
        },
        "filter2": {
          "doc_count": 1
        }
      }
    }


```



```java
private static  void filters(TransportClient client) {
        SearchRequestBuilder search = client.prepareSearch("mytest_1").setTypes("test").setSize(0);

        AggregationBuilder aggregation = AggregationBuilders.filters("filters",
                new FiltersAggregator.KeyedFilter("xiaomi",matchQuery("tag","小米")),
                new FiltersAggregator.KeyedFilter("daier",matchQuery("tag","戴尔"))
        );
        SearchResponse sr = search.addAggregation(aggregation).execute().actionGet();
        System.out.println(sr);
        Filters agg =sr.getAggregations().get("filters");
        agg.getBuckets().forEach(entry->{
            String key = entry.getKeyAsString();            // bucket key
            long docCount = entry.getDocCount();
            System.out.println("key : "+key+"\t docCount: "+docCount);
        });


    }
```







## 技术点

### 分词

#### 分词安装

https://github.com/medcl/elasticsearch-analysis-ik/tree/v5.5.0

#### 测试分词

```
GET _analyze
{
  "analyzer": "ik_smart",
  "text": ["中华人民共和国"]
  
}
```



### 参数

####boost:权重,缺省1.0

#### bool

bool 过滤 :可以用来合并多个过滤条件查询结果的布尔逻辑,它包含一下操作符:

must:多个查询条件的完全匹配,相当于 and。

must_not: 多个查询条件的相反匹配,相当于 not。

should: 至少有一个查询条件匹配, 相当于 or。			
​		

## 参考文档

https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/_structuring_aggregations.html