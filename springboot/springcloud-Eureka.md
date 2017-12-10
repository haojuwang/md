





# spring cloud

当我们用springboot 创建了很多微服务，我们怎么管理他们，怎么知道他们有没有宕机，各个服务器之间如何如访问，服务器之间的访问路径，接口的qps等等都可以用spring cloud 来帮助我们完成



#### spring cloud 有以下特点

* 分布式/版本化配置

* 服务器注册和发现

* 路由

* Service-to-service 调用

* 负载均衡

* 断路器

* 分布式消息传递

  ​







## 服务发现Eureka

#### 服务注册中心

当我们写好了几个服务之后想要统一的管理他们可以使用Eureka.



![spring-cloud-eureka](../images/spring-cloud-eureka.jpg)





##### 搭建服务器中心

1，pom.xml

```Xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.server</groupId>
	<artifactId>eureka</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>eureka</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Edgware.RELEASE</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>


</project>

```





2,Application

@EnableEurekaServer 注解启动一个服务注册中心给其他应用对话。

```Java
@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaApplication.class, args);
	}
}

```



3，application.properties

```
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      
      
security:
  user:
    name: xiaohaozi
    password: 123


```



* eureka.client.register-with-eureka:  由于该应用为注册中心，所以设置为false,代表不向注册中心注册自己。
* eureka.client.fetch-registry: 由于注册中心的职责就是维护实例，并不需要去检索服务，所以设置为false



4，http://127.0.0.1:8761/







#### 注册服务提供者

在完成了注册中心的搭建之后，接下来创建一个微服务spring boot应用加入到Eureka的服务治理体系中去。

1，pom.xml

```Xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>could</artifactId>
        <groupId>com.leyue</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>



    <modelVersion>4.0.0</modelVersion>

    <groupId>com.leyue.user</groupId>
    <artifactId>spuser</artifactId>
    <packaging>jar</packaging>

    <name>spuser</name>
    <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Edgware.RELEASE</spring-cloud.version>
    </properties>



    <dependencies>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka-server</artifactId>
        </dependency>




        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>


        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>


        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.1.1</version>
        </dependency>



    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```





2,Application 在主类中通过加上@EnableDiscoveryClient 注解，激活Eureka中的DiscoveryClient 实现。

```Java
@SpringBootApplication
@Configuration
@ComponentScan("com.leyue.user")
@EnableDiscoveryClient
public class SpUserApplication {

    public static void main(String[] arg) {
        SpringApplication.run(SpUserApplication.class,arg);


    }
}

```





3,application.properties

```
#[注册服务]

spring.application.name=spuer-user

eureka.client.service-url.defaultZone= http://xiaohaozi:123@192.168.0.106:8761/eureka/

eureka.instance.instance-id=${spring.application.name}:${vcap.application.instance_id:${spring.application.instance_id:${random.value}}}
```





#### Eureka 服务治理基础架构三个核心要素

* 服务注册中心：Eureka 提供的服务端，提供服务注册与发现的功能。
* 服务提供者： 提供服务的应用，可以是Spring boot应用，也可以是其他技术平台且遵循Eureka通信机制的应用。它将自己提供的服务注册到Eureka以供其他应用发现。
* 服务消费者：消费者应用从服务器中心获取服务列表，从而使消费者可以知道去何处调用其所需要的服务。





#### 客户端配置 eureka.client 为前缀



#### 服务器端参数 eureka.instance 为前缀





#### 安全访问

```
	<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
```



```
security:
  user:
    name: xiaohaozi
    password: 123
```



![spring-cloud-eureka](../images/spring-cloud-security.png)





##### 服务提供者修改地址

```
eureka.client.service-url.defaultZone= http://xiaohaozi:123@192.168.0.106:8761/eureka/

http://用户名:密码@192.168.0.106:8761/eureka/

消费者
http://xiaohaozi:123@spuer-user/user/find?name=
```

