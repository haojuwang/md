## zull 路由器和过滤器

spring-cloud-starter-zuul 依赖，该模块中不仅包含了Netflix Zull的核心依赖zuul-core,它还包含了：

* spring-cloud-starter-hystrix: 该依赖用来在网关服务中实现对微服务转发时候的保护机制，通过线程隔离和断路器，防止微服务的故障引发api网关资源无法释放，从而影响其他引用的对外服务。
* spring-boot-starter-actuator:该依赖用来提供常规的微服务管理断点，另外在spring cloud fuul 中还特别提供了/routes断点来返回当前的所有路由规则。



#### pom.xml

```xml
<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
		</dependency>


		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-zuul</artifactId>
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
```



#### 创建应用主类

使用@EnableZuulProxy注解开发zuul 的api 网关服务功能

```java
@EnableZuulProxy
@EnableEurekaClient
@SpringBootApplication
public class ZullApplication {

	public static void main(String[] args) {
		SpringApplication.run(ZullApplication.class, args);
	}
}

```



#### application.properties

```
server.port=9100

spring.application.name=service-zuul

eureka.client.service-url.defaultZone=http://leyue:leyue100@127.0.0.1:8761/eureka


zuul.routes.zhongan-url.path=/api-zhongan/**

zuul.routes.zhongan-url.serviceId=SERVER-ZHONGAN


zuul.routes.api-email.path=/api-email/**

zuul.routes.api-email.serviceId=SERVER-EMAIL


zuul.routes.service-feign.path=/service-feign/**

zuul.routes.service-feign.serviceId=SERVICE-FEIGN
```



![zull-serverid](../images/zull-serverid.png)

我们不需要再为各个路由微服务应用的具体实例的位置，而是通过简单的path 与 serviceId的映射组合，是的维护工作变得非常简单

```
http://127.0.0.1:9100/service-feign/sendMail
对应
http://127.0.0.1:6103/sendMail?
```



### 请求过滤

spring cloud zuul 核心功能：api 网关和请求过滤。

* 它作为系统的统一入口，屏蔽系统内部各个微服务的细节。
* 它可以与服务治理框架结合，实现自动化的服务实例维护以及负载均衡的路由转发。
* 它可以实现接口权限校验与微服务业务逻辑的解耦。
* 通过服务网关中的过滤器，在各生命周期中取校验请求的内容，将原本在对外服务层的校验前移，保证了微服务的无状态性，同时降低了微服务的测试难度，让服务本身更集中关注业务逻辑的处理



##### zuul 过滤器

一个简单的zuul过滤器，它实现了在请求被路由之前检查HttpServletRequest 中是否有accessToken 参数，若有就进行路由，若没有就拒绝访问，返回401 Unauthorized错误。

AccessFilter.java

```java
public class AccessFilter extends ZuulFilter {

    private static Logger logger = LoggerFactory.getLogger(AccessFilter.class);


    @Override
    public String filterType() {

        logger.error("--------filterType---------");
        return "pre";
    }

    @Override
    public int filterOrder() {
        logger.error("--------filterOrder---------");
        return 0;
    }

    @Override
    public boolean shouldFilter() {
        logger.error("--------shouldFilter---------");
        return true;
    }

    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        HttpServletRequest request = ctx.getRequest();
        logger.info("send {} request to }|"+request.getMethod()+request.getRequestURL().toString());

       Object accessToken = request.getParameter("accessToken");
       if(accessToken == null) {

           logger.warn("access token is empty");
           ctx.setSendZuulResponse(false);
           ctx.setResponseStatusCode(401);
           ctx.setResponseBody("access token is empty");


       }
        return null;
    }
}
```



* filterType: 过滤器的类型，它决定过滤器在请求的哪个声明周期中执行。这里定义为pre,代表会在请求被路由之前执行。
* filterOrder: 过滤器的执行顺序。当请求在一个阶段中存在多个过滤器时，需要根据该方法返回的值来一次执行。
* shoudFilter: 判断该过滤器是否需要被执行。返回了true,因此该过滤器对所有请求都会生效。实际运用中我们可以利用该函数来指定过滤器的有效范围。
* run: 过滤器的具体逻辑。这里通过ctx.setSendzuulReponse(false)令zuul过滤该请求，不对其进行路由，然后通过set.setResponseStatusCode(401)设置了返回的错误码，ctx.setResponseBody(body)返回的body内容进行编辑。



application设置

```java
@EnableZuulProxy
@EnableEurekaClient
@SpringBootApplication
public class ZullApplication {

	public static void main(String[] args) {
		SpringApplication.run(ZullApplication.class, args);
	}


	@Bean
	public AccessFilter accessFilter() {
		return new AccessFilter();
	}
}

```



#### 自定义路由映射规则



![](../images/zuul-pattens.png)







#### cookie 与头信息

默认情况下，Spring Cloud Zull在请求路由时，会过滤掉HTTP 请求头心心中的一些敏感信息，防止它们被传递到下游的外部服务器。默认的敏感头信息通过 zuul.sensitiveHeaders参数定义，包括

Cookie,Set-Cookie,Authorization

设置方法

```
# 方法一： 对指定路由开启自定义敏感头
zuul.routes.<router>.customSensitiveHeaders=true

# 方法二： 将指定路由的敏感头设置为空
zuul.routes.<router>.sensitiveHeaders=
```



#### 重定向问题

```
zuul.addHostHeader=true
```





#### 过滤器详解

在Spring Cloud Zuul 中实现的过滤器必须包含4个基本特征： 过滤类型，执行顺序，执行条件，具体操作。

```
    @Override
    public String filterType() {}
    
     @Override
    public int filterOrder() {}
    
    @Override
    public boolean shouldFilter() {}
    
    @Override
    public Object run() {}
```

* filterType: 该函数需要返回一个字符串来表示过滤器的类型，而这个类型就是在HTTP 请求过程中定义的各个阶段。在Zuul中默认定义了4中不同生命周期的过滤类型，
  * pre: 可以在请求被路由之前调用
  * routing:  在路由请求时被调用。
  * post： 在routing和error过滤器之后被调用。
  * error: 处理请求时发生错误时被调用。
* filterOrder:  通过int 值来定义过滤器的执行顺序，数值越小优先级越高。
* shouldFilter: 返回一个boolean 值来判断该过滤器是否要执行。我们可以通过此方法来指定过滤器的有效范围。
* run:  过滤器的具体逻辑。在该函数中，我们可以实现自定义的过滤逻辑，来确定是否要拦截当前的请求，不对其进行后续的路由，或是在请求路由返回结果之后，对结果做一些加工等。





![](../images/zull-order.png)















