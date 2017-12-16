

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
* <name> 这个项目用于展示的名称，一般在生成文档的时候使用
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





![依赖范围](../images/依赖范围.JPG)



##### 传递性依赖

每个依赖可能又有其他的依赖，其他依赖又有其他的依赖，循环往复。

maven 的传递性依赖，就是说会自动递归解析所有的依赖，然后负责将依赖下载下来，接着所有层级的依赖，都会成为我们的项目的依赖，不需要我们手工干预。所有需要的依赖全部下载下来，不管有多少层级，这个就是maven的传递性依赖机制，自动给我递归依赖链条下载所有的依赖的一个特性

我们对junit的依赖范围是test，junit对A的依赖范围是compile，那么我们对A的依赖范围是什么呢？ -> test





传递性依赖机制对依赖范围也是有影响的，比如下面的表格，第一列是一级依赖，第一行是二级依赖，传递性依赖会导致多级依赖的依赖范围交叉在一起，会有影响：

![传递性依赖影响范围](../images/传递性依赖影响范围.jpg)



有可能依赖是不会传递的，就是可能有些多层级的依赖，是不会成为我们项目的依赖的。





##### 依赖调解

maven 会自动解析所有层级的依赖，给我们自动下载所有的依赖，但是可能会出现依赖冲突的问题。

```
比如A->B->C->X(1.0)，A->D->X(2.0)，A有两个传递性依赖X，不同的版本

就产生了依赖冲突的问题，maven如何解决呢？依赖调解的机制

此时就会依赖调解，就近原则，离A最近的选用，就是X的2.0版本

如果A->B->X(1.0)和A->D->X(2.0)，路径等长呢？那么会选择第一声明原则，哪个依赖在pom.xml里先声明，就用哪个
```



##### 可选依赖

```
<optional> true </optional>
```

此时依赖传递失效，不会向上传递

```
如果A依赖于B，B依赖于C，B对C的依赖是optional，那么A就不会依赖于C。反之，如果没有optional，根据传递性依赖机制，A会依赖于C。
```



##### 总结

* 1，声明依赖
* 2，找依赖，两个方案去找到你需要的依赖的maven坐标
* 3，找到依赖后配置依赖，三要素，坐标。
* 4，依赖范围，根据依赖的使用场景，比如仅仅在test环境下使用，或者运行时由运行环境来提供，可以给依赖设置不同的依赖范围。
* 5，传递性依赖，自动解析所有层级的依赖。
* 6，传递性依赖的多层级的依赖范围，交叉影响的结果。
* 7，依赖调解，碰到一个项目多个版本，启动根据两大就近原则来选择一个最合适的版本来使用。
* 可选依赖，不向上传递。




## 第十三课 maven 依赖冲突

#### maven 依赖冲突的原因

```
当项目依赖了一个jar  这个又依赖了D 1.0版本   项目依赖了另一个jar 它却依赖了D2.0版本  
```

比如你依赖了A和B，此时A依赖了C-1.0，B依赖了D，D依赖了C-2.0

X

  -> A

-> C-1.0

  -> B

-> D

  -> C-2.0

X -> A -> C-1.0

X -> B -> D -> C-2.0

此时就会导致的事情是，由于A -> C1.0是最短路径，所以会用C1.0

但是坑爹的事情发生了，B依赖的是D，D依赖的C结果用了C-1.0版本

D本来用的是C-2.0的一个方法，但是现在给D的时C-1.0的一个类

比如C-1.0的类CClass.sayHello()

C-2.0给类CClass加了一些方法，比如CClass.printHello()，同时也有CClass.sayHello()方法

D调用了C-2.0里面的printHello()这个方法

如果用的时C-1.0，把C-1.0的CClass提供给D去用，D去调用printHello()方法的时候，就会报错。。。

C这个项目的CClass这个类的printHello()这个方法没有找到，not found的异常



#### 解决依赖冲突

1，先查看依赖的路径

```
mvn depedency:tree这个命令


com.maven:maven1:jar:1.0-SNAPSHOT
[INFO] +- org.springframework:spring-core:jar:3.2.8.RELEASE:compile
[INFO] |  \- commons-logging:commons-logging:jar:1.1.3:compile
[INFO] +- org.springframework:spring-webmvc:jar:3.2.8.RELEASE:compile
[INFO] |  +- org.springframework:spring-beans:jar:3.2.8.RELEASE:compile
[INFO] |  \- org.springframework:spring-expression:jar:3.2.8.RELEASE:compile
[INFO] +- org.springframework:spring-context:jar:3.2.8.RELEASE:compile
[INFO] +- org.springframework:spring-context-support:jar:3.2.8.RELEASE:compile
[INFO] +- org.springframework:spring-aop:jar:3.2.8.RELEAS
```

2,让其不包含依赖版本

````
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>3.2.8.RELEASE</version>
      <exclusions>
        <exclusion>
          <groupId>commons-logging</groupId>
          <artifactId>commons-logging</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
````





#### 总结

* 1，maven工作中最常见的依赖冲突问题的现象。
* 产生的原因是什么
* 解决的思路
* 具体用什么命令和配置去解决







## 第十五讲 nexus安装



#### 下载

http://nexus.sonatype.org/downloads/

https://help.sonatype.com/display/NXRM3/Download

nexus-3.6.1-02 : 这里包含nexus 运行需要的文件

sonatype-work: 包含nexus的配置文件，日志文件，仓库文件



#### 运行

nexus {start|stop|run|run-redirect|status|restart|force-reload}



#### 访问地址

http://10.211.55.3:8081/



#### 默认用户名密码

admin

admin123



#### 可以修改的配置文件

* nexus-3.6.1-02/bin/nexus.vmoptions 修改jvm参数

```
cat nexus.vmoptions
-Xms1200M
-Xmx1200M
-XX:MaxDirectMemorySize=2G
-XX:+UnlockDiagnosticVMOptions
-XX:+UnsyncloadClass
-XX:+LogVMOutput
-XX:LogFile=../sonatype-work/nexus3/log/jvm.log
-XX:-OmitStackTraceInFastThrow
-Djava.net.preferIPv4Stack=true
-Dkaraf.home=.
-Dkaraf.base=.
-Dkaraf.etc=etc/karaf
-Djava.util.logging.config.file=etc/karaf/java.util.logging.properties
-Dkaraf.data=../sonatype-work/nexus3
-Djava.io.tmpdir=../sonatype-work/nexus3/tmp
-Dkaraf.startLocalConsole=false
```



* nexus-3.6.1-02/etc/nexus-default.properties 修改端口 ip

  ```
  cat nexus-default.properties
  ## DO NOT EDIT - CUSTOMIZATIONS BELONG IN $data-dir/etc/nexus.properties
  ##
  # Jetty section
  application-port=8081
  application-host=0.0.0.0
  nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-requestlog.xml
  nexus-context-path=/

  # Nexus section
  nexus-edition=nexus-pro-edition
  nexus-features=\
   nexus-pro-feature
  ```

  ​



## 第十六讲 maven 仓库

#### maven 四种仓库

* Hosted: 宿主仓库
* proxy：代理仓库
* group: 仓库组
* 3rd party：宿主仓库



#### hosted:宿主仓库

```
hosted：宿主仓库，这个仓库，是用来让你把你公司内部的发布包部署到这个仓库里来，然后公司内的其他人可以从这个宿主仓库里下载依赖去使用。
```



#### proxy:代理仓库

```
proxy代理仓库，这个仓库不是用来给你公司内部的发布包部署的，是代理了公司外部的各种仓库，比如说java.net,codehaus,jboss仓库，最重要的是代理公司外部的中央仓库，但是这里其实可以修改为nexus连接的应该是国内的阿里云镜像仓库，阿里云去连接中央仓库。
```



#### group 仓库组

```
group仓库组，其实就是将，各种宿主仓库，代理仓库全部组成一个虚拟的仓库组，然后我们的项目只要配置依赖于一个仓库组，相当于就是可以自动连接仓库组对应的各种仓库。
```



#### maven-central

```
就是maven中央仓库的代理仓库。
```



#### maven-releases

```
该仓库是个宿主仓库，用于部署公司内部的release 版本的发布包(类似于1.0.0 release的意思你的工程已经经过了完善的测试，单元测试，集成测试,QA测试，上生产环境使用了)到这个仓库里面，供其他同事在生产环境依赖和使用。
```



#### maven-snapshots

```
该仓库是个宿主仓库，用于部署公司内部的snapshot版本的发布包到这个仓库(如果你的某个工程还在开发过程中，测试还没有结束，但是此时公司其他同时也在开发一些工程，需要依赖你的包进行开发和测试，联调，测试你的工程版本就是类似1.0.0-Snapshot这样的版本),供其他同事在开发和测试的时候使用。
```



#### 3rd party

```
该仓库是个宿主仓库，主要用来部署没法从公共仓库获取的第三方依赖包，比如说，你的公司依赖于第三方支付厂商的一个依赖包，那个依赖包不是开源的，是商业的包，那么你是没有办法从maven中央仓库获取的，此时，我们可能会自己手动从支付厂商哪里获取到一个jar包，下载之后上传到私服里来，就放这个仓库里，3rd-party仓库。
```







#### nexus仓库架构图

![nexus仓库架构](../images/nexus仓库架构.png)







## 第十七讲 搭建各种仓库



#### maven-public 已有

#### maven-central代理仓库

```
maven-central代理仓库：从直接代理maven中央仓库，修改为代理阿里云仓库，
http://maven.aliyun.com/nexus/content/groups/public
```

#### maven-snapshots 宿主仓库已有

#### maven-releases宿主仓库已有

#### 创建3rd-party仓库



![3rd-party_1](../images/3rd-party_1.png)



![3rd-party_1](../images/3rd-party_2.png)



![3rd-party_1](../images/3rd-party_3.png)





![3rd-party_1](../images/3rd-party_4.png)





## 第十八讲 强制依赖从私服下载

#### 在 .m2/setting.xml 中设置

```Xml


   <mirror>
    <id>nexus</id>
    <mirrorOf>*</mirrorOf>
    <url>http://10.211.55.3:8081/repository/maven-public/</url>
  </mirror>



<profile>
    <id>nexus</id>
        <repositories>
            <repository>
                <id>central</id>
                <name>Nexus </name>
              <url>http://10.211.55.3:8081/repository/maven-public/</url>
                <releases><enabled>true</enabled></releases>
                <snapshots><enabled>true</enabled></snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>central</id>
                <name>Nexus Plugin Repository</name>
            <url>http://10.211.55.3:8081/repository/maven-public/</url>
                <releases><enabled>true</enabled></releases>
                <snapshots><enabled>true</enabled></snapshots>
            </pluginRepository>
        </pluginRepositories>
  </profile>
  </profiles>

  <activeProfiles>
  <activeProfile>nexus</activeProfile>
</activeProfiles>
```



* id设置为  repository id 设置为 central 和  pluginRepository id 设置为central的目的就是覆盖掉默认的中央仓库maven-model-builder-3.5.2.jar
* url 设置为 nexus maven-public 的地址。
* mirror 是让所有的依赖下载的url地址走私服nexus





##第十九讲 权限管理机制创建部署专用账号

nexus的权限是典型的RBAC模型，role-based access control。 每个用户可以分配多个多个角色，每个角色分配多少个权限，每个权限就是一个具体的功能，比如浏览依赖，部署发布包。

Nexus 默认有三个用户：

```
admin 管理员账号，admin123

deployment，可以搜索和部署构建，就是普通的开发账号，密码是deployment123(在nexus 3.x最新版本已经被撤销掉了)

anonymous: 如果没有给认证信息，就是这个匿名账号，可以下载依赖，查看依赖
```



#### 创建一个专门用来部署的账号 depoyment

```
1,涵盖所有匿名账号的权限，至少可以搜索仓库，下载依赖。
2，对仓库有所有的权限管理权限，就可以往仓库中去部署发布包。
```



#### 创建角色

![3rd-party_1](../images/create_role.png)





#### 创建用户

![3rd-party_1](../images/create_user.png)







## 第二十讲 本地项目jar 部署到nexus私服

#### 1,发布仓库配置

将项目发布包部署到哪个仓库中，是需要用下面的pom.xml中的配置来设置

```Xml
    <distributionManagement>
        <repository>
            <id> nexus-releases</id>
            <name> Nexus Release Repository</name>
            <url>http://10.211.55.3:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id> nexus-snapshots</id>
            <name> Nexus Snapshot Repository</name>
            <url>http://10.211.55.3:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```

```
<version>1.0-SNAPSHOT</version> 会部署到nexus-snapshots仓库
<version>1.0</version> 会部署到nexus-releases仓库
```



![](../images/version.png)



#### 2,部署专用账号的配置

nexus仓库对于普通的匿名用户是只读的，也就是说，只要下载依赖，不能部署发布包，因此如果要能够部署发布包，还需要在settings.xml文件里通过<server>元素配置使用专用的部署用户，来通过认证，进行发布包的部署。

需要在settings中配置：

```xml
  <server>
    <id>nexus-releases</id>
    <username>deployment</username>
    <password>123456</password>
  </server>
  <server>
    <id>nexus-snapshots</id>
    <username>deployment</username>
    <password>123456</password>
  </server>
```



#### 3,执行maven deploy 命令部署到私服

mvn clean deploy命令，就可以让maven自动给我们编译源代码，运行单元测试，打成jar包，将jar包安装到本地仓库，将jar包部署到配置的远程仓库里面去。

在私服上面，如果上传snapshot版本的jar包时会自动带一个时间戳，这个主要是因为考虑到在开发过程中会频繁的部署snapshot包，所以会用时间戳来区分一下，但是这个对我们是透明的，如果我们团队里其他同时要用你的这个snapshot jar包，直接还是用你的坐标来生命依赖就可以了，它是不用去考虑这个时间戳的问题的，私服会替你管理好。





#### maven 命令的说明

```
mvn clean package: 清理，编译，测试，打包.
```

```
mvn clean install: 清理，编译，测试，打包，安装到本地仓库，比如你自己负责了3个工程的开发，相互之间有依赖，那么如果你开发好其中一个工程，需要在另外一个工程中医用它，此时就需要将开发好的工程jar包安装到本地仓库，然后才可以在另外一个工程声明对他的依赖，此时会直接取用本地仓库中的jar.
```

```
mvn clean deploy:清理，编译，测试，打包，安装到本地仓库，部署到远程私服，这个其实就是你负责的工程写好了部分代码，别人需要依赖你的jar包中提供的接口来写代码和测试。此时你需要将snapshot jar包发布到私服的maven-snapshots仓库中。供给人在本地生命对你的依赖和使用
```



#### 3rd-party 将jar包部署到nexus中

##### 1，在settings.xml 加入

```Xml
<server>
    <id>nexus-3rd-party</id>
    <username>deployment</username>
    <password>123456</password>
  </server>
```

2,通过mvn 命令上传文件

```Java
 mvn deploy:deploy-file -DgroupId=com.csource -DartifactId=fastdfs-client-java -Dversion=1.24 -Dpackaging=jar -Dfile=/Users/lixueqin/fastdfs_client_v1.24.jar -Durl=http://10.211.55.3:8081/repository/3rd-party/ -DrepositoryId=nexus-3rd-party

```



* 注意：-DrepositoryId=nexus-3rd-party 一定要和settings.xml 的id 一致





## 第二十二讲 maven生命周期和plugin执行原理

#### 图解mave生命周期以及执行原理

![](../images/maven_生命周期.png)







```
maven 生命周期，就是去解析mvn 各种命令背后的原理
```

```
清理，初始化，编译，测试，打包，集成测试，验证，部署，站点生成。
```

```
maven 有三套完全独立的生命周期，clean default 和site.每套生命周期都可以独立运行，每个生命周期的运行都会包含多个phase，每个phase又是各种插件的goal来完成的，一个插件的goal可以认为是一个功能。

这就是maven的生命周期-> phase(可以理解为阶段)->插件的关系，也是maven构建执行的核心原理。
```



```
你每次执行一个生命周期，都会依赖执行这个生命周期内部的多个phase,每个phase执行时都会执行某个插件的goal完成具体的功能
```



#### maven的生命周期以及phase

* Clean 生命周期包含的phase如下

  ```
  pre-clean
  clean
  post-clean
  ```

  ​

* Default 生命周期包含的phase如下：

  ```
  validate：校验这个项目的一些配置信息是否正确

  initialize：初始化构建状态，比如设置一些属性，或者创建一些目录

  generate-sources：自动生成一些源代码，然后包含在项目代码中一起编译

  process-sources：处理源代码，比如做一些占位符的替换

  generate-resources：生成资源文件，主要是去处理各种xml、properties那种配置文件，去做一些配置文件里面占位符的替换

  process-resources：将资源文件拷贝到目标目录中，方便后面打包

  compile：编译项目的源代码

  process-classes：处理编译后的代码文件，比如对java class进行字节码增强

  generate-test-sources：自动化生成测试代码

  process-test-sources：处理测试代码，比如过滤一些占位符

  generate-test-resources：生成测试用的资源文件

  process-test-resources：拷贝测试用的资源文件到目标目录中

  test-compile：编译测试代码
  process-test-classes：对编译后的测试代码进行处理，比如进行字节码增强

  test：使用单元测试框架运行测试

  prepare-package：在打包之前进行准备工作，比如处理package的版本号

  package：将代码进行打包，

  pre-integration-test：在集成测试之前进行准备工作，比如建立好需要的环境

  integration-test：将package部署到一个环境中以运行集成测试

  post-integration-test：在集成测试之后执行一些操作，比如清理测试环境

  verify：对package进行一些检查来确保质量过关

  install：将package安装到本地仓库中，这样开发人员自己在本地就可以使用了

  deploy：将package上传到远程仓库中，这样公司内其他开发人员也可以使用了
  ```

  ​

* site 生命周期的phase

  ```
  pre-site
  site
  post-size
  site-deploy
  ```

  ​



#### 默认的phase和plugin绑定

我们直接运行mvn clean package 的时候，每个phase都是由插件的goal来完成的，phase和plugin绑定关系是什么样的？

实际上，默认maven就绑定了一些plugin goal到phase到上去了，

类似于resources:resources 这种格式，说的就是resources这个plugin的resources

```
processs-resources  resources:resources

compile 	complier:compile

process-test-resources			resources:testResources

test-compile					compiler:testCompile

test								surefire:test

package							jar:jar或者war:war

install							install:install

deploy							deploy:deploy
```



* Site 生命周期的默认绑定是

  site 			site:site

  Site-deploy		site:deploy

  ​

* Clean 生命周期的默认

  clean   clean:clean



#### maven 的命令和生命周期

* 比如 mvn clean package

clean 是指的clean 生命周期中的clean phase

Package 是指的default生命周期中的package phase

此时就会执行clean生命周期，在clean phase之前的所有phase和clean phase    pre-clean ,clean

同时会执行defalut 生命周期中，在package phase之前的所有phase和package phase



* Mvn clean package 

  ​	clean生命周期的pre-clean ,clean 两个phase

  但是 ，pre-clean默认是没有绑定任何一个plugin goal的，所以默认什么也不会干；clean phase默认是绑定了 clean:clean,clean plugin的clean goal，所以就会执行clean插件的clean goal 就会实现一个功能，清理target目录下的文件 





````
mvn dependency:tree
mvn deploy:deploy-file

这就是不执行任何一个生命周期的任何一个phase,直接执行指定的插件的一个goal.
dependency:tree 就是直接执行dependency 这个插件的tree 这个goal,这个意思就是会自动分析pom.xml 里面的依赖生命，递归解析所有的依赖，然后打印出一颗依赖树。

mvn deploy:deloy-file,就是直接执行deploy这个插件的deploy-file这个goal,这个意思就是说将制定项目的jar包，以指定的坐标，部署到指标的maven私服仓库里去，同时使用指定仓局id对应的server的账号和密码
````







## 第二十三讲 插件配置

#### 1，plugin和goal

```
plugin
每个插件都有多个goal,每个goal 都是一个具体的功能
例如： dependency插件有十几个goal,可以进行分析项目依赖，列出依赖树

插件的goal,写法就是plugin:goal,比如dependency:tree

用mvn plugin:goal 就可以手动执行某个插件的goal,执行某种功能
```



#### 2，将插件的goal绑定到phase上

maven 内置就会绑定一些插件的goal到phase上，如何手动将插件的goal 绑定到phase上？

将source插件的jar-no-fork goal 绑定到verif  phase,在完成集成测试之后，就生成源码的jar包

 ```Xml
 
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <version>2.1.1</version>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <phase>verify</phase>
                        <goals>

                            <goal>jar-no-fork</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
 ```



* 把goals绑定到phase上面
* 运行 mvn verify
* http://maven.apache.org/plugins/index.html 常用插件




## 第二十六讲 多模块统一构建

```
maven 聚合功能就是将各个模块，聚合成一个大的模块，给它一个父工程，那个父模块里面聚合了所有的子模块

只要对父模块运行一次构建命令，此时maven 会自动对这个模块下面的所有子模块都运行响应的构建命令，这样就可以保证一键自动化构建所有的模块，不要一个一个依次去构建。
```

如果要一次性构建多个模块的工程，那么就需要创建一个父工程，我们可以创建一个os-parent工程

```xml
  <groupId>com.oa</groupId>
  <artifactId>oa-parent</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
  <modules>

    <module>oa_flow</module>
    <module>oa_organ</module>
    <module>oa_auth</module>

  </modules>
```



* packaging 设置为pom






## 第二十七讲 dependencyManagemnent和pluginManagement

* dependencyManagement 语法

```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId></groupId>
            <artifactId></artifactId>
           <version></version>
        </dependency>
    </dependencies>
</dependencyManagement>
```



* pluginManagement 语法

```
<pluginManagement>
    <plugins>
        <plugin>
            
        </plugin>
    </plugins>
</pluginManagement>
```



将所有工程相同依赖和插件的配置，全部放入父工程中，此时子工程就会继承父工程依赖和插件，不需要每个工程都重复定义，而且可以在父工程中统一约束所有依赖和插件的版本。

但是这里有个问题，如果直接将所有依赖和插件直接放入父工程，会导致子工程强制性继承，也许自工程并不需要某些依赖呢？

此时最好的做法，就是在父工程中，使用<dependencyManagement>元素和<pluginManagement>元素，来声明所有的依赖和插件。

此时在工程中，就可以对需要的依赖进行声明，但是不用定义版本号，只要groupId和artifactId即可。这样声明之后才会继承依赖，而且版本由父工程约束。

```
dependencyManagement和pluginManagement的意义在哪？一般来说，很多人都没搞清楚用上面说的那两个元素进行继承，以及直接在parent里面放dependencies 和 plugins里面放依赖和插件来继承，他们俩的区别在哪？

如果在父工程中，直接用dependencies 和 plugins来声明依赖和插件，子工程会全部强制继承；如果用dependencyManagement和pluginManagement来声明依赖和插件，默认情况下，子工程什么都不继承，只有当子工程声明了某个依赖或者插件的groupId+ artifactId,但是不指定版本时，才会从父工程继承那个依赖。在规范的工程管理中，肯定都是用dependencyManagement和pluginManagement的。
```





## 第二十八讲 版本号统一控制

```
一般都会在properties元素里面，统一定义一个版本号，然后在这个类似spring的依赖里面，全部用${} 占位符来引用一个版本号，那么每次修改，升级版本，就直接修改properties元素中的一个地方就可以了，不会出错。
```

```xml
   <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <spring.version>3.2.8.RELEASE</spring.version>
    </properties>
    
     <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-core</artifactId>
          <version>${spring.version}</version>
     </dependency>
```





## 第三十讲 环境搭建

```
   <!-- oa系统的几个模块依赖 -->
        <dependency>
            <groupId>com.oa</groupId>
            <artifactId>oa_organ</artifactId>
            <version>${version}</version>
        </dependency>
        <dependency>
            <groupId>com.oa</groupId>
            <artifactId>oa_auth</artifactId>
            <version>${version}</version>
        </dependency>
        <dependency>
            <groupId>com.oa</groupId>
            <artifactId>oa_flow</artifactId>
            <version>${version}</version>
        </dependency>

```







## 第三十一讲 surefire 插件和cobertura插件

#### surefire 插件单元测试

```
maven 中默认内置了surefire插件来运行单元测试，与最流行的junit单元测试框架整合非常好。一般是在default生命周期的test阶段，会运行surefire插件的test goal ,然后执行src/test/java下面的所有单元测试的。
surefire 插件会根据一定的规则在sre/test/java下面找单元测试类，具体规则如下：

**/Test*.java
**/*Test.java
**/*TestCase.java

通常比较流行的是用*Test.java格式类命名单元测试的类。
```

##### 自定义包含与排除测试类

````Xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-plugin</artifactId>
  <version>2.5</version>
  <configuration>
    <includes>
      <include>**/*Tests.java</include>
    </includes>
    <excludes>
      <exclude>**/*TempTest.java</exclude>
    </excludes>
  </configuration>
</plugin>
````



#### cobertura插件 测试覆盖率的报告 

```Xml
<plugin>  
                <groupId>org.codehaus.mojo</groupId>  
                <artifactId>cobertura-maven-plugin</artifactId>  
                <version>2.5.1</version>  
            </plugin>

oa-web.pom
 <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>cobertura-maven-plugin</artifactId>
            </plugin>
```



##### 执行命令

```
mvn cobertura:cobertura

报告在 target/site/cobertura/下面
```






## 第三十二讲 jetty 插件使用

#### pom 加入jetty插件配置

```Xml
<plugin>
	<groupId>org.mortbay.jetty</groupId>
	<artifactId>jetty-maven-plugin</artifactId>
	<version>7.1.6.v20100715</version>
	<configuration>
		<scanIntervalSeconds>10</scanIntervalSeconds>
		<webAppConfig>
			<contextPath>/test</contextPath>
		</webAppConfig>
	</configuration>
</plugin>
```



* scanIntervalSeconds 指的是自动扫描项目代码变更的时间
* contextPath 部署之后的contextPath

Jetty 插件的groupId 不是默认的org.apache.maven.plugins，因此如果要执行该插件，需要在settings.xml 中进行配置：

```
<pluginGroups>
	<pluginGroup>org.mortbay.jetty</pluginGroup>
</pluginGroups>
```



```
settings.xml文件里，有一个东西叫做pluginGroups

就是我们一般如果配置了插件后，然后如果我们就直接在命令行运行jetty:run cobertura:cobertura命令，其实用的是插件缩写，jetty 插件 cobertura插件，但是如果要将插件的缩写和插件自己的groupId对应起来的话有的时候还是需要你手动配置一下的，不然在某些环境下可能直接运行类似jetty:run的命令可能会有问题
```



* 运行命令

  ```
  mvn jetty:run
  ```

  默认端口是8080

* mvn jetty:run -Djetty.port=8081可以修改端口号





## 第三十三讲 cargo插件 发布war

#### tomcat设置tomcat-user.xml

```Xml
<role rolename="manager"/>
<role rolename="manager-gui"/>
<role rolename="admin"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<role rolename="admin-gui"/>

<user username="admin" password="admin" roles="admin,manager,manager-gui,manager-script,manager-jmx,manager-status,admin-gui"/>

```



```Xml
如无新建conf/Catalina/localhost/manager.xml 内容如下：
<Context privileged="true" antiResourceLocking="false"
         docBase="${catalina.home}/webapps/manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
</Context>
```



#### 中文乱码

* 修改tomcat的conf/server.xml，里面在8080那个地方，加入两个配置，URIEncoding="UTF-8" useBodyEncodingForURI="true"

* web.xml

  ```Xml

    <!-- 字符集编码过滤器 -->
    <filter>
      <filter-name>encodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
      </init-param>
      <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
      </init-param>
    </filter>
    <filter-mapping>
      <filter-name>encodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
    </filter-mapping>
  ```

  ​

#### cargo插件使用 基于tomcat8

* 1，settings.xml

  ```
  <pluginGroup>org.codehaus.cargo</pluginGroup>
  ```

* 2,生成war

  ```
  运行mvn clean package命令，打包，必须先有一个war包
  ```

* 3,Pom.xml

  ```Xml
     <!-- 自动化部署远程Tomcat插件 -->
              <plugin>
                  <groupId>org.codehaus.cargo</groupId>
                  <artifactId>cargo-maven2-plugin</artifactId>
                  <version>1.4.13</version>
                  <configuration>
                      <container>
                          <containerId>tomcat8x</containerId>
                          <type>remote</type>
                      </container>
                      <configuration>
                          <type>runtime</type>
                          <properties>
                              <cargo.remote.username>admin</cargo.remote.username>
                              <cargo.remote.password>admin</cargo.remote.password>
                              <cargo.tomcat.manager.url>http://10.211.55.3:7070/manager</cargo.tomcat.manager.url>
                              <cargo.servlet.port>7070</cargo.servlet.port>
                              <cargo.hostname>10.211.55.3</cargo.hostname>
                              <cargo.tomcat.ajp.port>8009</cargo.tomcat.ajp.port>
                          </properties>

                            </configuration>
                </configuration>
            </plugin>
  ```

* 4,执行命令
 ```
mvn cargo:deploy 
 ```

* 5，重新部署

 ```
 mvn cargo:redeploy
 
  mvn cargo:redeploy
 ```


## 第三十四讲  profile 自动适配环境修改配置文件



#### 常见的开发环境



```
dev:即本地开发和测试环境，这个就是指的是我们项目本地的笔记本电脑，在上面可以进行开发，单元测试，冒烟测试。比如说你的local本地环境需要有一些基础性的依赖设施，比如说数据库，比如说MQ，比如说redis。这些东西，mysql、rabbit mq、redis，都是部署在公司的一套公共的一个dev环境，有一套服务器，上面会部署各种项目组成员在本地开发，需要的各种依赖设施。保证你在本地开发的时候，指定对应的地址，都是可以连通，笔记本电脑连通的。

```


```
beta: beata 一般称作内部测试环境，也就是不对外。集成测试/联调测试的环境，一般自己感觉开发好了之后，就需要将自己的系统部署到一个集成测试环境，说白了就是一个项目都是多个研发人员搞得，每个人弄好了自己的部分，都要发布到一个环境，大家在上面测一测整个系统多个服务能不能串起来跑的通，至少别报错，这一块主要是确保主流程要跑通，不要报错。
```


```
test:QA 测试环境，正经项目，都会有专门的测试人员，如果没有的话，那研发人员自己充当QA角色了，就是需要将通过集成测试的代码，部署到QA测试环境，然后由QA人员进行非常充分而且完善的测试，验证功能性能，等各个方面都没有问题
```


```
staging： 预发布环境，通常这个环境会跟生产环境保持基本一致，部署到上面后，就会使用部分真实的流量或者数据，来让系统运行。比如对外提供服务的网站，app之类的，可以通过流量拷贝的方式，拷贝一小部分流量过来，在staging环境让开发好的系统跑一跑。后者也可以拷贝部分真实的线上数据库的数据下来，跑一跑。然后这个环节QA还是会介入，再次验证一下看系统是否运行正常。同时这个环节，有一个验收的作用，项目如果有产品经理，此时会在这个环节看一下是否符合他的产品预期。
```


```
prod: 生产环境，最终系统部署到线上生产环境中，完成上线
```



每个环境，都有完全隔离开来，都有自己的数据库，mq,缓存等等各种各样的依赖，比如数据库把，使用的肯定也是不同的数据库。



dev环境下，通常是用的开发人员自己本地安装的一个数据库，或者是用公司测试环境中专门用于开发的一个公共测试数据库；dev环境下，有一个专门的集成测试数据；test环境下，有一个专门的QA 测试数据库；staging环境下，有一个专门的预发环境数据库；prod环境下，就是线上的生产环境数据库。



#### 基于资源过滤+profile的方式适配各个环境

比如resources下的jdbc.properties

* 替换文件标签

```
  jdbc.url = ${jdbc.url}

  jdbc.driver = ${jdbc.driver}

  jdbc.username= ${jdbc.username}

  jdbc.password=${jdbc.password}

```



* Pom.xml 配置 在project 标签结束之前

```xml

      <profiles>
          <profile>
              <id>local</id>
              <activation>
                  <activeByDefault>true</activeByDefault>
              </activation>
              <properties>
                  <jdbc.driver>com.mysql.jdbc.Driver</jdbc.driver>
                  <jdbc.url>jdbc:mysql://localhost:3306/arch1?useUnicode=true&amp;characterEncoding=UTF-8</jdbc.url>
                  <jdbc.username>root</jdbc.username>
                  <jdbc.password></jdbc.password>
              </properties>
          </profile>
          <profile>
              <id>dev</id>
              <properties>
                  <jdbc.driver>com.mysql.jdbc.Driver</jdbc.driver>
                  <jdbc.url>jdbc:mysql://45.76.97.203:3306/oa?useUnicode=true&amp;characterEncoding=UTF-8</jdbc.url>
                  <jdbc.username>root</jdbc.username>
                  <jdbc.password>lihao123</jdbc.password>
              </properties>
          </profile>
      </profiles>
```

  ​

* 执行测试命令

  ```
   mvn clean process-resources -Pdev
  ```

  在target/classes下面可以看到被替换的文件

* 打包时替换

  ```
  mvn clean package -Pdev，-P就是说激活dev profile
  ```

  ​

  ​

* 默认激活

  ```Xml
   <activation>
                  <activeByDefault>true</activeByDefault>
              </activation>
  ```

  ​

* 查看默认激活

  ```
  mvn help:active-profiles
  ```





#### 目录替换

```
在大型的工程里面，配置文件特别多，你不可能把所有的配置都放profiles里面，那个有点乱，一般是怎么做呢？src/main/resources下面直接就不放东西了，实际上是profile里面定义一个单属于自己的环境的一套配置文件对应的目录，然后在处理文件的时候，会把激活的profile对应的一套目录里的资源文件拷贝到src/main/resources下面去，给工程打包使用。

再多创建几个目录
src/main/profiles/dev
src/main/profiles/beta
src/main/profiles/test
src/main/profiles/staging
src/main/profiles/prod
```



* pom.xml设置

  ```Xml
   <profiles>
          <profile>
              <id>local</id>
              <activation>
                  <activeByDefault>true</activeByDefault>
              </activation>
              <build>
                  <resources>

                      <resource>
                          <directory>src/main/profiles/local</directory>
                          <includes>
                              <include>**/*.xml</include>
                              <include>**/*.properties</include>
                          </includes>
                          <filtering>true</filtering>
                      </resource>

                  </resources>

                 </build>
        </profile>

        <profile>
            <id>dev</id>
            <build>
                <resources>

                    <resource>
                        <directory>src/main/profiles/dev</directory>
                        <includes>
                            <include>**/*.xml</include>
                            <include>**/*.properties</include>
                        </includes>
                        <filtering>true</filtering>
                    </resource>

                </resources>
               </build>
        </profile>
    </profiles>   
    </build>
        </profile>

        <profile>
            <id>dev</id>
            <build>
                <resources>

                    <resource>
                        <directory>src/main/profiles/dev</directory>
                        <includes>
                            <include>**/*.xml</include>
                            <include>**/*.properties</include>
                        </includes>
                        <filtering>true</filtering>
                    </resource>

                </resources>
                 </build>
        </profile>
  ```





* 运行命令

  ```
  mvn process-resources -Pdev
  ```





#### 真实环境


第一步，oa-organ，oa-auth，oa-flow，三个模块的负责人，此时用的snapshot版本，mvn clean deploy -Pbeta，此时就会用beta环境的东西去打包和部署到私服，给别人去用

第二步，oa-web，要部署beta的tomcat，此时依赖的还是各个工程的snapshot版本，此时各个工程的最新的snapshot版本对应的Jar包里面就都是beta环境的配置文件了

第三步，oa-web，cargo发布到哪个环境的配置，也是可以用profiles下面的properties来进行占位符替换的，每个profile定义一个properties，里面包含自己的环境对应的tomcat地址，然后在cargo插件的配置里面，用占位符来引用即可。

第四步，oa-web，打一个war包，此时也是mvn clean package -Pbeta，用beta环境的配置打成war包，而且此时用的都是另外三个模块的beta环境下的jar包，打在他自己的war包里面

第五步，oa-web，用cargo，mvn cargo:redeploy -Pbeta，此时就可以进行beta环境的部署

第六步，大家在各个环境如法炮制全都测试完了，此时呢就会上线。各个依赖模块，用mvn clean deploy -Pprod，去打一个prod环境的jar包，部署到私服；oa-web，打出来一个war包，此时打包的时候，用的都是各个依赖最新的prod环境的jar包，接着就使用mvn cargo:redeploy -Pprod，此时就会发布到prod环境对应的tomcat地址上去。
```xml

<!-- 自动化部署远程Tomcat插件 -->
			<plugin>
				<groupId>org.codehaus.cargo</groupId>
				<artifactId>cargo-maven2-plugin</artifactId>
				<version>1.0</version>
				<configuration>
					<container>
						<containerId>tomcat6x</containerId>
						<type>remote</type>
					</container>
					<configuration>
						<type>runtime</type>
						<properties>
							<cargo.remote.username>${prop.cargo.remote.username}</cargo.remote.username>
							<cargo.remote.password>${prop.cargo.remote.password}</cargo.remote.password>
							<cargo.tomcat.manager.url>${prop.cargo.tomcat.manager.url}</cargo.tomcat.manager.url>
							<cargo.servlet.port>${prop.cargo.servlet.port}</cargo.servlet.port>
						</properties>
					</configuration>
				</configuration>
			</plugin>

------------------------------------------------------------------------------------------

<profiles>
		<profile>
			<id>dev</id>
			<activation>
				<activeByDefault>true</activeByDefault>
			</activation>
			<properties>
				<prop.cargo.remote.username>admin</prop.cargo.remote.username>
				<prop.cargo.remote.password>admin</prop.cargo.remote.password>
				<prop.cargo.tomcat.manager.url>http://localhost:8080/manager</prop.cargo.tomcat.manager.url>
				<prop.cargo.servlet.port>8080</prop.cargo.servlet.port>
			</properties>
		</profile>
		
		<profile>
			<id>beta</id>
			<properties>
				<prop.cargo.remote.username>admin-beta</prop.cargo.remote.username>
				<prop.cargo.remote.password>admin-beta</prop.cargo.remote.password>
				<prop.cargo.tomcat.manager.url>http://localhost:8080/manager-beta</prop.cargo.tomcat.manager.url>
				<prop.cargo.servlet.port>8080</prop.cargo.servlet.port>
			</properties>
		</profile>
		
		<profile>
			<id>test</id>
			<properties>
				<prop.cargo.remote.username>admin-test</prop.cargo.remote.username>
				<prop.cargo.remote.password>admin-test</prop.cargo.remote.password>
				<prop.cargo.tomcat.manager.url>http://localhost:8080/manager-test</prop.cargo.tomcat.manager.url>
				<prop.cargo.servlet.port>8080</prop.cargo.servlet.port>
			</properties>
		</profile>
		
		<profile>
			<id>staging</id>
			<properties>
				<prop.cargo.remote.username>admin-staging</prop.cargo.remote.username>
				<prop.cargo.remote.password>admin-staging</prop.cargo.remote.password>
				<prop.cargo.tomcat.manager.url>http://localhost:8080/manager-staging</prop.cargo.tomcat.manager.url>
				<prop.cargo.servlet.port>8080</prop.cargo.servlet.port>
			</properties>
		</profile>
		
		<profile>
			<id>prod</id>
			<properties>
				<prop.cargo.remote.username>admin-prod</prop.cargo.remote.username>
				<prop.cargo.remote.password>admin-prod</prop.cargo.remote.password>
				<prop.cargo.tomcat.manager.url>http://localhost:8080/manager-prod</prop.cargo.tomcat.manager.url>
				<prop.cargo.servlet.port>8080</prop.cargo.servlet.port>
			</properties>
		</profile>
	</profiles>
```





## maven 下载源码命令

````
mvn dependency:sources 
````







## 常用插件

### 1,shade 设置运行主类 并打包相关的依赖



* 插件设置

```Xml
<!--设置主类-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <manifestEntries>
                                <Main-Class>${app.main.class}</Main-Class>

                            </manifestEntries>
                        </transformer>
                    </transformers>

                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```

* profile

```Xml
  <profiles>
        <profile>
            <id>main</id>
            <properties>
                <app.main.class>com.oa.illness.Main</app.main.class>
            </properties>

        </profile>
    </profiles>
```



* 命令

  ```
  mvn clean package -Pmain     

  java -jar  target/oa_illness-1.0-SNAPSHOT.jar

  ```

  ​

  ​