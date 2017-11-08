# solr 使用

## core 操作

#### 新建core

```
./solr create -c tests
```

#### 查询core

```
/v2/cores
```

#### 删除core

```
http://10.10.10.1:8984/solr/admin/cores?action=UNLOAD&core=test2&deleteIndex=true&deleteDataDir=true&deleteInstanceDir=true
```

#### 查看命令

```
http://10.10.10.1:8984/v2/cores/_introspect
```

#### 重命名

```
http://10.10.10.1:8984/solr/admin/cores?action=RENAME&core=tests&other=test
```



#### 创建core

```
http://10.10.10.1:8984/solr/admin/cores?action=CREATE&name=newcore3&instanceDir=newcore&config=solrconfig.xml&dataDir=data

admin/cores?action=RELOAD&core=newcore5

-Dsolr.allow.unsafe.resourceloading=true

bin/solr start -Dsolr.allow.unsafe.resourceloading=true -force

./solr start -p 8984  -Dsolr.allow.unsafe.resourceloading=true -force

http://10.10.10.1:8984/solr/admin/cores?action=CREATE&name=newcore5&instanceDir=newcore5&config=/home/solr-7.1.0/server/solr/template/solrconfig.xml&schema=/home/solr-7.1.0/server/solr/template/managed-schema&dataDir=data

```





## 搜索

```
if( !$solr->ping() ) {
    echo'Solr server not responding';
   exit;
}
```

### q

```
　“:”  指定字段查指定值，如返回所有值*:*

　　“?”  表示单个任意字符的通配

　　“*”  表示多个任意字符的通配（不能在检索的项开始使用*或者?符号）

　　“~”  表示模糊检索，如检索拼写类似于”roam”的项这样写：roam~将找到形如foam和roams的单词；roam~0.8，检索返回相似度在0.8以上的记录。

　　AND、||  布尔操作符

　　OR、&&  布尔操作符

　　NOT、!、-（排除操作符不能单独与项使用构成查询）

　　“+”  存在操作符，要求符号”+”后的项必须在文档相应的域中存在²

　　( )  用于构成子查询

　　[]  包含范围检索，如检索某时间段记录，包含头尾，date:[201507 TO 201510]

　　{}  不包含范围检索，如检索某时间段记录，不包含头尾date:{201507 TO 201510}
　　
　　不能添加对关键词添加""
　　
　　id:lft1T3NO1a1 OR  id:lV5iIulcuf7
　　http://www.cnblogs.com/zhangweizhong/p/5056884.html
```

### fq 过滤

```
（filter query）过虑查询，提供一个可选的筛选器查询。返回在q查询符合结果中同时符合的fq条件的查询结果，例如：q=id:1&fq=sort:[1 TO 5]，找关键字id为1 的，并且sort是1到5之间的。

字符串类型要添加""
hType:"综合医院"
```





![fq](/Users/lihao/Desktop/md/images/fq.png)







```
$query = new Query();
$data = new QueryData();
$data->coreName = 'hospital';
//$data->q = "hName:昆山中医";
$data->fq=['hType:"综合医院"','hLevel:"三级甲等"','hProvince: "江苏"'];
$data = $query ->query($data);
```



### sort

```
　　sort  排序方式，例如id  desc 表示按照 “id” 降序
　　Order desc, Doctors asc
```





## 新版本中文分词



**1、**进入解压好的solr文件夹根目录下执行以下命令

https://github.com/fnpac/IKAnalyzer2017_6_6_0

```
https://github.com/fnpac/IKAnalyzer2017_6_6_0
```

2 ,schema

```
<!-- IKAnalyzer -->
<fieldType name="IK_cnAnalyzer" class="solr.TextField" positionIncrementGap="100">
    <analyzer type="index">
        <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="false"/>
        <filter class="org.wltea.analyzer.lucene.IKTokenFilterFactory" useSingle="true" useItself="false" />
    </analyzer>
    <analyzer type="query">
        <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="false"/>
        <filter class="org.wltea.analyzer.lucene.IKTokenFilterFactory" useSingle="true" useItself="false" />
    </analyzer>
</fieldType>
```

