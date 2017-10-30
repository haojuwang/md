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
　　
　　
　　id:lft1T3NO1a1 OR  id:lV5iIulcuf7
　　http://www.cnblogs.com/zhangweizhong/p/5056884.html
```

