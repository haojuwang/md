# Solr
## Solr是什么
	Solr是apache的顶级开源项目，它是使用java开发 ，基于lucene的全文检索服务器。
	Solr比lucene提供了更多的查询语句，而且它可扩展、可配置，同时它对lucene的性能进行了优化

#### Solr是如何实现全文检索的
* 索引流程：solr客户端（浏览器、java程序）可以向solr服务端发送POST请求，请求内容是包含Field等	信息的一个xml文档，通过该文档，solr实现对索引的维护（增删改）。
* 搜索流程：solr客户端（浏览器、java程序）可以向solr服务端发送GET请求，solr服务器返回一个xml	文档。

## Solr 和lucene 的区别
	Lucene是一个全文检索引擎工具包，它只是一个jar包，不能独立运行，对外提供服务。

	Solr是一个全文检索服务器，它可以单独运行在servlet容器，可以单独对外提供搜索和索引功能。
	Solr比lucene在开发全文检索功能时，更快捷、更方便。

## Solr安装配置
#### 下载Solr
	http://archive.apache.org/dist/lucene/solr/

#### 目录结构
![]img/(Solr1.png)	
![]img/(Solr2.png)
![]img/(Solr3.png)

#### 3.3.1	Solr的安装部署
* 第一步：安装tomcat
* 第二步 将solr.war包，拷贝到tomcat的webapps目录下
* 第三步：解压缩war包解压缩之后，将war包删掉
* 第四步：添加solr的扩展服务包
![]img/(Solr4.png)
将以上jar包，添加到以下目录
![]img/(Solr5.png)

* 第五步：添加log4j.properties 拷贝example》resources 到solr 的Web-INf>classes
* 第六步：在web.xml中指定solrhome的目录
![]img/(Solr6.png)

#### Solrcore的安装

##### Solrcore和solrhome
	Solrhome是solr服务运行的主目录，一个solrhome目录里面包含多个solrcore目录，一个solrcore目录里面了一个solr实例运行时所需要的配置文件和数据文件。

	每一个solrcore都可以单独对外提供搜索和索引服务。
	多个solrcore之间没有关系。

##### Solrcore和solrhome的目录结构	
	Solrhome的目录结构
![]img/(Solr7.png)	
	Solrcore目录
![]img/(Solr8.png)

#####	Solrcore的安装
	安装solrcore需要先安装solrhome
	将以下目录的文件进行拷贝	 到solrhome 文件夹下面
	![]img/(Solr9.png)
	![]img/(Solr10.png)

这样solrhome和solrcore就安装成功了。	

#####	Solrcore配置
在solrcore的conf目录下，有一个solrconfig.xml的配置文件，该配置文件，配置来solrcor的运行信息
在该文件中，主要配置三个标签：lib标签、datadir标签、requestHandler标签

如果对该文件不进行配置也可以，即使用默认的配置项。

######	Lib 标签
* Solrcore需要添加一个扩展依赖包，通过lib标签来指定依赖包的地址
* solr.install.dir：表示solrcore的安装目录
将以下目录的文件进行拷贝
![]img/(Solr11.png)
复制到以下目录
![]img/(Solr12.png)
修改lib标签
![]img/(Solr13.png)

####### datadir标签
	每个SolrCore都有自己的索引文件目录 ，默认在SolrCore目录下的data中。
	如果不想使用默认的目录也可以通过solrConfig.xml更改索引目录 ，如下：
![]img/(Solr14.png)

####### requestHandler标签
	requestHandler请求处理器，定义了索引和搜索的访问方式。
* 通过/update维护索引，可以完成索引的添加、修改、删除操作。
	<requestHandler name="/update" class="solr.UpdateRequestHandler">
* 通过/select搜索索引。
	 <requestHandler name="/select" class="solr.SearchHandler">
   
     <lst name="defaults">
        <str name="echoParams">explicit</str>
        <int name="rows">10</int><!--显示数量-->
        <str name="wt">json</str><!--显示格式-->
        <str name="df">text</str><!--默认搜索字段-->
    </lst>


------------------------------------------------------------------

## solr 界面介绍
![]img/(Solr15.png)

#### Dashboard
	仪表盘，显示了该Solr实例开始启动运行的时间、版本、系统资源、jvm等信息。

#### Logging
	Solr运行日志信息

#### Cloud	
	Cloud即SolrCloud，即Solr云（集群），当使用Solr Cloud模式运行时会显示此菜单.

#### Core Admin	
	Solr Core的管理界面。在这里可以添加SolrCore实例。

#### java properties
	Solr在JVM 运行环境中的属性信息，包括类路径、文件编码、jvm内存设置等信息。

#### Tread Dump	
	显示Solr Server中当前活跃线程信息，同时也可以跟踪线程运行栈信息。

#### 	Core selector	
	选择一个SolrCore进行详细操作

![]img/(Solr16.png)	

##### Analysis 
![]img/(Solr17.png)	
	通过此界面可以测试索引分析器和搜索分析器的执行情况。
	注：solr中，分析器是绑定在域的类型中的。

#####	dataimport
	可以定义数据导入处理器，从关系数据库将数据导入到Solr索引库中。
	默认没有配置，需要手工配置。

##### Document	
	通过/update表示更新索引，solr默认根据id（唯一约束）域来更新Document的内容，如果根据id值搜索不到id域则会执行添加操作，如果找到则更新。

	通过此菜单可以创建索引、更新索引、删除索引等操作
	overwrite="true" ： solr在做索引的时候，如果文档已经存在，就用xml中的文档进行替换
	commitWithin="1000" ： solr 在做索引的时候，每个1000（1秒）毫秒，做一次文档提交。为了方便测试也可以在Document中立即提交，</doc>后添加“<commit/>”

##### Query
	通过/select执行搜索索引，必须指定“q”查询条件方可搜索


## 多solrcore的配置
配置多solrcore的好处：
* 在进行solrcloud的时候，必须配置多solrcore	.
* 每个solrcore之间是独立的，都可以单独对外提供服务。不同的业务模块可以使用不同的solrcore来提供搜索和索引服务。

#### 添加solrcore
* 第一步：复制solrhome下的collection1目录到本目录下，修改名称为collection2
* 第二步：修改solrcore目录下的core.properties
* 第三步：重启tomcat

使用命令 
./bin/solr create -c logsys_clinic_analyes -force
http://blog.csdn.net/matthewei6/article/details/50620600

solr 重启
./solr restart -m 2g -force

## Solr的基本使用

### Schema.xml
	在schema.xml文件中，主要配置了solrcore的一些数据信息，包括Field和FieldType的定义等信息，在solr中，Field和FieldType都需要先定义后使用。

#### Filed
	<field name="id" type="string" indexed="true" stored="true" required="true" multiValued="false" /> 
	Name：指定域的名称
	Type：指定域的类型
	Indexed：是否索引
	Stored：是否存储
	Required：是否必须
	multiValued：是否多值，比如商品信息中，一个商品有多张图片，一个Field像存储多个值的话，必须将multiValued设置为true。

#### dynamicField	
	动态域
	<dynamicField name="*_i"  type="int"    indexed="true"  stored="true"/>
	Name：指定动态域的命名规则

#### uniqueKey	
	指定唯一键
	<uniqueKey>id</uniqueKey>
    其中的id是在Field标签中已经定义好的域名，而且该域要设置为required为true。
	一个schema.xml文件中必须有且仅有一个唯一键

#### copyField	
	复制域
	  <copyField source="product_name" dest="product_keywords" />
	Source：要复制的源域的域名
	Dest：目标域的域名

	由dest指的的目标域，必须设置multiValued为true。

#### FieldType	
	定义域的类型
	<fieldType name="text_general" class="solr.TextField" positionIncrementGap="100">
      <analyzer type="index">
        <tokenizer class="solr.StandardTokenizerFactory"/>
        <filter class="solr.StopFilterFactory" ignoreCase="true" words="stopwords.txt" />
        <!-- in this example, we will only use synonyms at query time
        <filter class="solr.SynonymFilterFactory" synonyms="index_synonyms.txt" ignoreCase="true" expand="false"/>
        -->
        <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
      <analyzer type="query">
        <tokenizer class="solr.StandardTokenizerFactory"/>
        <filter class="solr.StopFilterFactory" ignoreCase="true" words="stopwords.txt" />
        <filter class="solr.SynonymFilterFactory" synonyms="synonyms.txt" ignoreCase="true" expand="true"/>
        <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
    </fieldType>

    Name：指定域类型的名称
	Class：指定该域类型对应的solr的类型
	Analyzer：指定分析器
	Type：index、query，分别指定搜索和索引时的分析器
	Tokenizer：指定分词器
	Filter：指定过滤器

## 中文分词
使用ikanalyzer进行中文分词
* 第一步 将ikanalyzer的jar包拷贝到以下目录
![]img/(Solr18.png)	
* 第二步：将ikanalyzer的扩展词库的配置文件拷贝到 目录
![]img/(Solr19.png)
* 第三步：配置FieldType

	<fieldType name="text_ik" class="solr.TextField">     
	  <analyzer type="index" useSmart="false"
	        class="org.wltea.analyzer.lucene.IKAnalyzer" />
	    <analyzer type="query" useSmart="true"
	        class="org.wltea.analyzer.lucene.IKAnalyzer" />   
	</fieldType> 

* 第四步：配置使用中文分词的Field
	  <field name="product_name" type="text_ik" indexed="true" stored="true" multiValued="false" />

* 第五步：重启tomcat


## Dataimport
	该插件可以将数据库中指定的sql语句的结果导入到solr索引库中。

* 	第一步：添加jar包
	* Dataimport的jar包 复制以下目录的jar包
![]img/(Solr20.png)	
	添加到以下目录
![]img/(Solr21.png)		
	修改solrconfig.xml文件，添加lib标签
	`<lib dir="${solr.install.dir:../..}/contrib/dataimporthandler/lib" regex=".*\.jar" />`
	* MySQL数据库驱动包
		将mysql的驱动包，复制到以下目录
		![]img/(Solr22.png)	
		修改solrconfig.xml文件，添加lib标签
		`<lib dir="${solr.install.dir:../..}/contrib/db/lib" regex=".*\.jar" />`

* 第二步：配置requestHandler		
	在solrconfig.xml中，添加一个dataimport的requestHandler
	<requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">
    <lst name="defaults">
      <str name="config">data-config.xml</str>
    </lst>
    
  </requestHandler>
 * 第三步：创建data-config.xml
 	在solrconfig.xml同级目录下，创建data-config.xml

 * 第四步：	重启tomcat	

	
## solr 常用命令
### solr  命令所在位置
	已下载solr文件夹bin下面

### 启动solr
	./solr restart 重启项目
	./solr start   启动
	./solr start -p 8984 指定端口启动
	./solr restart -m 2g -force 指定占用多大内存
#### 在浏览器输入：http://127.0.0.1:8983/solr 即可 	

### 创建solr core
 	./solr create -c newcore(core名称) -force 刷新网页可以查看新建的core
	
### 删除core
	./solr delete -c newcore12(core名称)

### 删除core里面所有的数据
	post -c abc(core名称) -d "<delete><query>*:*</query></delete>"	

### 停止solr 
	 ./solr stop -all

### 查看solr状态
	./solr staus

### 帮助
	./solr help

------------------------------------------------------
2016.12.19































