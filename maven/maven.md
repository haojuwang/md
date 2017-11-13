

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

