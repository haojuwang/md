## swagger 是什么

swagger 是一个规范和完整的框架，用于生成，描述，调用和可视化RESTful风格的web服务。

Springfox 的前身是swagger-springmvc，是一个开源的api doc框架，可以将我们的Controller的方法以文档的形式展现，基于Swagger.



#### 添加jar

```xml
 <!-- Swagger -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.6.0</version>
</dependency>

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.6.0</version>
</dependency>
```





#### 配置

```java
@Configuration
@EnableSwagger2
public class Swagger2Configuration {

    @Bean
    public Docket accessToken() {
        return new Docket(DocumentationType.SWAGGER_2).groupName("api") //定义组
                .select() //选择那些路径和api会生成document
                .apis(RequestHandlerSelectors.basePackage("com.leyue.controller"))
                .paths(regex("/api/.*")) //拦截的接口路径
                .build() //创建
                .apiInfo(apiInfo()); //配置说明
    }

    private ApiInfo apiInfo() {
            return new ApiInfoBuilder()
                    .title("****") //标题
                    .description("负责****参数加解密")//描述
                    .termsOfServiceUrl("http://www.***.com")
                    .contact(new Contact("lihao","http://www.le***.com","***@**.com"))
                    .version("1.0")//版本
                    .build();
    }


}
```



#### 测试

```
 http://localhost:8080/swagger-ui.html
```





#### 示例

```java
@Api(description = "描述", tags = {"tag"})
@RestController
@RequestMapping("/api")
public class EncryptController {

    private Logger logger = LoggerFactory.getLogger(EncryptController.class);

    @Autowired
    EncryptService encryptService;

    @ApiOperation(value = "加密")
    @ApiImplicitParams(value={
            @ApiImplicitParam(name = "parms1", required = true, paramType = "query", dataType = "String", value = "参数1"),
            @ApiImplicitParam(name = "parms2", required = true, paramType = "query", dataType = "String", value = "参数2")
    })
    @ApiResponse(code = 200,message = "message")
    @PostMapping(value = "encrypt")
    public String encrypt(String parms1, String parms2) {


        return "";
    }

```



#### 安全设置

```xml
  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

```



application.properties

```
security.basic.enabled=true
security.basic.path=/swagger-ui.html
security.user.name=username
security.user.password=password
```





#### 自定义

* @Api: 用在类上，说明该类的作用

  ```
  @Api(description = "描述", tags = {"tag"})
  ```

* @ApiImplicitParams: 用在方法上面包含一组参数说明。

* @ApiReponses: 用于表示一组响应。

* @ApiResponse:用在@ApiResponses 中,一般用于表达一个错误的响应信息

  code:数字,例如 400

  message:信息,例如"请求参数没填好"response:抛出异常的类

* @ApiModel:描述一个 Model 的信息(这种一般用在 post 创建的时候,使用@RequestBody 这样的场
  景,请求参数无法使用@ApiImplicitParam 注解进行描述的时候)

* @ApiModelProperty:描述一个 model 的属性

  ​			
  ​		https://github.com/swagger-api/swagger-core/wiki/Annotations
  ​	

  ​


  ​				
  ​			
  ​		
  ​	