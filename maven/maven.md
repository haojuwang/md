

## 第四讲

* Maven 安装下载maven

  http://mirror.bit.edu.cn/apache/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz

* 配置环境变量

  ```
  export M2_HOME=/Users/lixueqin/maven
  export PATH=$PATH:$M2_HOME/bin
  ```

*  查看是否配置成功

  ```
  mvn -v
  ```

  ​

#### maven的配置文件

```
maven 有一个重要的配置文件，就是settings.xml ，这个文件默认是在%M2_HOME%/conf目录下面，但是如果升级maven的版本，那么可能导致新的安装包的settings文件一片空白。
所以一般maven的配置文件都会放在当前用户的目录下的 ~/.m2/settings.xml中，这样就是对当前用户有效。
```





## 第五讲

#### 设置mavn 设置aliyun镜像

在settings.xml 的标签<mirrors> 中添加如下配置

```
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>

```



#### 用maven 命令创建工程

```
mvn archetype:generate -DgroupId=com.zhss.maven -DartifactId=maven-first-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```



* -DartifactId 项目目录

#### maven的约定

在maven 项目工程目录结构中，其中src/main/java 目录包含了这个项目的java 源码 src/test/java 目录包含了测试代码，pom.xml文件是maven的核心配置文件，是这个项目的Project Object Model



#### pom.xml 介绍

```Xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.zhss.maven</groupId>
  <artifactId>maven-first-app</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>maven-first-app</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

```

* <project>: pom.xml的顶层元素
* <ModelVersion> POM 本身的版本号，一般很少变化
* <groupId> 创建这个项目的公司或者组织，一般用公司网站后缀，比如com.company，或者cn.company，或者org.zhonghuashishan
* <artifactId> 这个项目的唯一标识，一般生成的jar包名称，会是 <artifactId>-<version>.<packaging> 比如说 myapp-1.0.jar
* <packaging>要用的打包类型，比如jar,war 等等。
* <version>  这个项目的版本号
*  <name> 这个项目用于展示的名称，一般在生成文档的时候使用
* <url> 这个项目的文档能下载的站点url,一般用于生成文档
* <description>：用于项目的描述



#### 对项目进行打包

使用mvn package命令，对一个工程进行构建，构建出来一个可以执行的java jar包。

会先下载依赖的包，然后运行 单元测试 编译 打包，自动运行了单元测试的用例，自动化把你的java 源代码编译成了.class 文件，自动化把我们的项目打包成了一个jar包。



#### 执行打包好的jar包

```
java  -cp maven-first-app-1.0-SNAPSHOT.jar com.zhss.maven.App
```

com.zhss.maven.App  是带有main方法的类名







## 第六讲 maven 体系架构

项目中的pom.xml 依赖以及如何构建

全局的maven配置，settings.xml

maven的约定：各种约定目录，代码、资源，输出，测试



#### mvn 构建命令执行过程

* 1，maven 一定会查看setting.xml 各种配置文件
* 2，解析项目中的pom.xml
* 3,mvn 会解析出pom.xml 中的依赖
* 4，查看本地仓库是否有pom.xml中的依赖
* 5，本地仓库在 ~/.m2/repository，先去本地仓库去查找如果没有依赖就去远程仓库查找
* 6，远程仓库下载后缓存到本地仓库，远程仓库位置到%M2_HOME%/lib下面找，maven-model-builder-3.5.2.jar，https://repo.maven.apache.org/maven2/

下载依赖->本地仓库->远程仓库

![maven 架构图](../images/maven 架构图.png)







## 第七讲安装eclipse

设置meven 目录

![maven 架构图](../images/eclipse.png)





## 第十讲 maven 知识点

每个maven项目都有一个坐标

```
groupId + artifactId + version + packageing + classifier 五个维度的坐标，唯一定位一个依赖包 
```

```
实际开发中只需要 groupId + artifactId + version 来定位一个唯一依赖
```

```
groupId 公司或者组织 官网域名倒叙来开头
com.zhss.hr
com.zhss.finance
com.zhss.crm
```

```
artifactId: 项目中的某个模块或者某个服务
com.zhss.oa，oa-organ，organ是缩写，organization
com.zhss.oa，oa-auth，auth是authorization
com.zhss.oa，oa-flow，flow就是流程的意思

```

```
version：这个工程的版本号
```



#### 设置坐标的作用

```
groupId + artifactId +version 就成了这一次这个工程目前这个状态的唯一的标识和定位。
将这个版本的代码的jar包，上传到maven的中央仓库，供其他人来使用这个版本的代码的jar包
给公司其他人来使用，只有你也有一个坐标，才能让别人唯一定位你的项目某个版本的代码的jar包，让maven下载后给别人用
```



#### 坐标的思路

1，坐标有哪些元素

2，坐标每个元素都是什么意思，怎么设置

3，设置了坐标之后的意义在哪







## 第十一讲 eclipse 结合maven 使用



## 第十二课 maven 依赖管理

#### 用<dependency> 可以引用任何你需要的依赖

```xml
  		<dependency>  
            <groupId></groupId>  
            <artifactId></artifactId>  
            <version></version>  
            <type></type>  
            <scope></scope>  
            <optional></optional>
          	
        </dependency>  
```



#### 去哪里找需要的依赖

* 官网

* maven 仓库

  ```
  http://search.maven.org/
  http://repository.sonatype.org
  http://www.mvnbrowser.com/
  http://mvnrepository.com/
  ```

  ​

#### 依赖范围

```
<scope></scope>
```

Maven 有三套classpath,classpath 就是项目中用到的各种依赖的类，jvm在运行的时候需要去classpath 下面加载对应的类：

* 1，主代码编译时一套classpath。

* 2，编译测试代码以及执行测试代码的时候一套classpath

* 3,   被打包运行的时候一套classpath

  依赖范围就是用来控制依赖包与这三种classpath的关系的。

不同的依赖范围，会导致那个依赖包可能在编译，测试或者打包运行的时候，有时候可以使用，有时候不能够使用。

##### 依赖范围的值

```
compile ： 默认，对主代码编译，测试，运行都有效。一般都是用这种scope
```

```
test: 仅仅对于运行测试代码的classpath有效，对于主代码或者打包运行的时候无效，仅仅测试代码需要用的依赖一般都会设置为这个范围，比如junit,这种返回的依赖不会出现在最终打包发布包里面的，减少发布包的体积。
```

```
provided:对主代码和测试代码有效，但是在运行的时候无效，因为可能环境已经提供了，比如servlet-api。
```

```
runtime:测试和运行classpath 有效，但是编译代码时无效，比如jdbc的驱动实现类，比如mysql驱动。因为写代码的时候基于javax.sql包下的标准接口去写代码
```







![传递性依赖影响范围](../images/传递性依赖影响范围.jpg)

















![依赖范围](../images/依赖范围.JPG)





