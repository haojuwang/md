## 数据库密码特殊字符处理
	URLEncoder.encode(pass, "utf-8")

## 运行jar main 传递参数
#### sh
	#!/bin/sh
	/usr/bin/java -jar test.jar $1 $2	

#### java
	 public static void main(String []arg){
        if(arg.length>0) {
            for(String str :arg) {
                System.out.println("man :"+str);
            }
        }
    }

#### 命令行
	./test.sh "李浩" "hello world"

## 中文分词
http://blog.csdn.net/tjcyjd/article/details/43453007
配置的过程中出现了一些小问题，一下将详细讲下IK Analyzer的配置过程，配置非常的简单，但是首先主要你的Solr版本是哪个，如果是3.x版本的用IKAnalyzer2012_u6.zip如果是4.x版本的用IK Analyzer 2012FF_hf1.zip，一定要对应上，要不然会配置失败

下载地址为：http://code.google.com/p/ik-analyzer/downloads/list

#### 拷贝配置文件。
解压压缩包将IKAnalyzer.cfg.xml、stopword.dic拷贝至需要使用分词器的core的conf下面，和core的schema.xml文件一个目录。

#### 修改schema.xml配置文件。
	<fieldType name="text_ik" class="solr.TextField">     
	     <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>     
	</fieldType> 
	<field name="my_name" type="text_ik" indexed="true" stored="true" multiValued="false" />  
#### 重启tomcat



## mysql 命令行乱码
	set names 'utf8';






