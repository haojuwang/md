# 声明式服务调用：Spring Cloud Feign

由于RestTemplate 封装，几乎每一个调用都是简单的模板化的内容，Spring Cloud Feign在此基础上做了进一步封装，由它来帮助我们定义和实现依赖服务接口的定义，在Spring Cloud Feign的实现下，我们只需创建一个接口并用注解的方式配置它，即可完成对服务提供方的接口绑定，简化了再使用Spring Cloud Ribbon时自行封装服务调用客户端的开发量，Spring Cloud Feign具备可插拔的注解支持包括Feign注解和JAX-RS注解。



## 创建使用

* 1，在pom 中引入eureka 和feign

```Xml

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Edgware.RELEASE</spring-cloud.version>
    </properties>


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>




        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>


        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-ribbon</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-feign</artifactId>
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





* 2,创建主类FeignApplication 并通过@EnableFeignClients 注解开启Spring Cloud Feign的支持功能

  ```Java
  @SpringBootApplication
  @EnableDiscoveryClient
  @EnableFeignClients
  public class FeignApplication {
      public static void main(String[] arg) {

          SpringApplication.run(FeignAoolication.class,arg);

      }
  }

  ```

* 3,定义Service 接口，通过@FeignClent 注解指定服务器名来绑定，然后在使用Spring mvc 注解来绑定具体该服务提供的REST接口

  ​

  ```Java
  //不区分大小写
  @FeignClient("SPUER-USER")
  public interface UserService {

      @RequestMapping(value = "/user/find",method = RequestMethod.GET)
      String getUser(@RequestParam("name") String name);
  }
  ```

  ​



* 4，RestController

  ```java
  @RestController
  @RequestMapping("consumer")
  public class ConsumerController {


      private Logger logger = LoggerFactory.getLogger(ConsumerController.class);

      @Autowired
      private UserService userService;


      @RequestMapping("user")
      public String user(String name) {
         return userService.getUser(name);
      }


  }

  ```





* 5，application 配置

  ```

  server.port=8002


  logging.level.com.leyue.consumer= debug


  spring.application.name=user-feign

  eureka.client.service-url.defaultZone= http://xiaohaozi:123@192.168.0.106:8761/eureka/
  ```

  ​

















