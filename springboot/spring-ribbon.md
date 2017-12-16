



# 客户端负载均衡Ribbon

负载均衡在系统架构中是一个非常重要，并且是不得不去实施的内容。因为负载均衡是对系统的高可用，网络压力的缓解和处理能力扩容的重要手段之一。

客户端负载均衡和服务端负载均衡最大的不同点在于上面所提到的清单所存储的位置。在客户端负载均衡中，所有客户端节点都维护着自己要访问的服务端清单，而这些服务端的清单来自于服务注册中心。



通过Spring cloud ribbon 的封装，我们在微服务架构中使用客户端负载均衡调用非常简单，只需要如下两步：

* 服务提供者只需要启动多个服务实例并注册到一个注册中心或是多个相关的服务注册中心。
* 服务消费者直接通过被@LoadBanced注解修饰过的RestTemplate来实现面向服务的接口调用。

这样，我们就可以将服务提供者的高可用以及服务消费者的负载均衡调用一起实现了。





## 具体实现

1，在Eureka 注册两个一样名称的spring.application.name

2,在application 中添加 注解@LoadBanced

```Java
@SpringBootApplication
@Configuration
@EnableDiscoveryClient
@RibbonClient(name = "spuer-user", configuration = TestConfiguration.class)
public class ConsumerApplication {

    public static void main(String[] arg) {
        SpringApplication.run(ConsumerApplication.class,arg);


    }


    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

```





#### 负载均衡器

* LoadBalancerClient 可以获取到要求的那个具体的服务提供者

* 所有的均衡器都实现了ILoadBalancer

  ​

![cloud-balancer](../images/cloud-balancer.png)









#### 负载均衡策略

![cloud-balancer](../images/cloud-rule.png)



使用负载均衡策略时不要和Application 在同一个包中。

TestConfiguration

```Java
@Configuration
public class TestConfiguration {

//    @Autowired
//    IClientConfig config;

    @Bean
    public IRule ribbonRule(IClientConfig config) {
        return new RandomRule();
    }



}
```



如果想实现更细的粒度的客户端可以配置RibbonClient

```Java
@SpringBootApplication
@Configuration
@EnableDiscoveryClient
@RibbonClient(name = "spuer-user", configuration = TestConfiguration.class)
public class ConsumerApplication {

    public static void main(String[] arg) {
        SpringApplication.run(ConsumerApplication.class,arg);


    }


    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

```





controller

```Java
@RestController
@RequestMapping("consumer")
public class ConsumerController {

    @Autowired
    RestTemplate restTemplate;


    private Logger logger = LoggerFactory.getLogger(ConsumerController.class);

    @Autowired
    private LoadBalancerClient loadBalancerClient;


    @RequestMapping("user")
    public String user(String name) {
       return restTemplate.getForObject("http://xiaohaozi:123@spuer-user/user/find?name="+name,String.class);
    }


    @GetMapping("balacner")
    public Map<String,String> balacner() {
        ServiceInstance service = this.loadBalancerClient.choose("spuer-user");

        logger.error(service.getHost());
        logger.error(service.getUri().toString());
        logger.error(String.valueOf(service.getPort()));

        return service.getMetadata();
    }

//    @Autowired
//    private EurekaClient discoveryClient;
//
//
//    @RequestMapping("serviceUrl")
//    public String serviceUrl() {
//        InstanceInfo instance = discoveryClient.getNextServerFromEureka("SPUER-USER", false);
//
//        return instance.getHomePageUrl();
//    }




```



