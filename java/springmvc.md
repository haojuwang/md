##web.xml
字符转码
设置DispathcherServlet
配置spring xml
设置对应的url

##spring.xml
handlermapping
handleradapter
mvc:annotation-driven
扫描
bean
viewresolver

##导入jar
com.springsource.com.mchange.v2.c3p0-0.9.1.2.jar
com.springsource.org.aopalliance-1.0.0.jar
com.springsource.org.apache.commons.logging-1.1.1.jar
com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
jstl-1.2.jar
mysql-connector-java-5.1.28-bin.jar
spring-aop-4.3.5.RELEASE.jar
spring-aspects-4.3.5.RELEASE.jar
spring-beans-4.3.5.RELEASE.jar
spring-context-4.3.5.RELEASE.jar
spring-context-support-4.3.5.RELEASE.jar
spring-core-4.3.5.RELEASE.jar
spring-expression-4.3.5.RELEASE.jar
spring-jdbc-4.3.5.RELEASE.jar
spring-orm-4.3.5.RELEASE.jar
spring-test-4.3.5.RELEASE.jar
spring-tx-4.3.5.RELEASE.jar
spring-web-4.3.5.RELEASE.jar
spring-webmvc-4.3.5.RELEASE.jar


##web.xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
             http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
             version="3.1">

    <!--post 字符转码-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <!--springmvc servlet-->
    <servlet>
        <servlet-name>spmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
         <init-param>
             <param-name>contextConfigLocation</param-name>
             <param-value>classpath:spmvc-servlet.xml</param-value>
         </init-param>
        <load-on-startup>1</load-on-startup>

    </servlet>
    <servlet-mapping>
        <servlet-name>spmvc</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>


    </web-app>

##springmvc.xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.0.xsd
         http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
       ">
 <context:component-scan base-package="com.springmvc.contoller"></context:component-scan>
    <!--包含mapping adapter json-->
    <mvc:annotation-driven/>

    <bean id="myController" name="/hello.do" class="com.springmvc.contoller.HelloWord"></bean>
    <bean name="/toadd.do" class="com.springmvc.contoller.ToAdd"></bean>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--<property name="prefix" value="/WEB-INF/jsps/"></property>-->
        <property name="suffix" value=".jsp"></property>

    </bean>
    </beans>

## RestFull 方式
#### web.xml添加配置
    <servlet-mapping>
        <servlet-name>spmvc</servlet-name>
        <url-pattern>/rest/*</url-pattern>
    </servlet-mapping>

#### 示例代码
    @Controller
    @RequestMapping("restfull")
    public class RestFullController {
        //http://localhost:8484/springmvc/rest/restfull/update/id/3/name/lihao
        @RequestMapping("/update/id/{id}/name/{name}")
        public String update(Model model,@PathVariable Integer id,@PathVariable     String name){
            User user =  new User();
            user.setId(id);
            user.setUsername("lihao");
    
            model.addAttribute("name",user);
    
            return "demo";
    
        }

        //http://localhost:8484/springmvc/rest/restfull/add/3/lihao
        @RequestMapping("add/{id}/{name}")
        public String add(Model model,@PathVariable Integer id,@PathVariable String name){
            User user =  new User();
            user.setId(id);
            user.setUsername("lihao");
            
            model.addAttribute("name",user);

            return "demo";
        }
    }


## 转发 url地址不改变
    @RequestMapping("forward")
    public String forward() {
        return "forward:/kuaiyi/param.do";
    }

## 重定向 url地址改变
    @RequestMapping("redirect")
    public String redirect() {
        return "redirect:/kuaiyi/param.do";
    }


## springmvc 返回json
#### 添加jar jackson-core-2.5.0.jar
#### springmvc.xml配置
     <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
        <property name="messageConverters">
            <list >
                <ref bean="mappingJacksonHttpMessageConverter" />
            </list>
        </property>
    </bean>
    <bean id="mappingJacksonHttpMessageConverter"
          class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
        <property name="supportedMediaTypes">
            <list>
                <value>text/html;charset=UTF-8</value>
            </list>
        </property>
    </bean>

#### 示例代码
    @RequestMapping("tojson")
    @ResponseBody
    public User toJson(String name,String sex,String address,String birthday) {
        User user = new User();
        user.setUsername(name);
        user.setSex(sex);
        user.setAddress(address);
        user.setBirthday(birthday);
        System.out.println(user);
        return user;
    }

    @RequestMapping("mapjson")
    @ResponseBody
    public Map<String, String> mapJson(String name) {
        HashMap<String, String> map = new HashMap<>();
        map.put("name", name);
        return map;
    }    
----------------------------------------------------
    <script type="text/javascript">
    //String name,int age,String address,String birthday
    function json() {
        $.ajax({
            type:'POST',
            url:'${pageContext.request.contextPath}/json/tojson.do',
            data:'name=李浩&sex=男&address=北京&birthday=1992-02-08',
            success:function(data){
                console.log(data)
                alert(data.username);
            }
        })
    }

    </script>


## oscache
#### 添加jar
    oscache-2.4.1.jar
#### 添加 oscache.properties配置
    cache.memory=false
    <!-- 缓存文件个数 -->
    cache.capacity=1000
    cache.persistence.class=com.opensymphony.oscache.plugins.diskpersistence.DiskPersistenceListener
    cache.path=/Users/lihao/Downloads/log


#### 全局配置 缓存页面 web.xml
     <filter>
        <filter-name>oscache</filter-name>
        <filter-class>com.opensymphony.oscache.web.filter.CacheFilter</filter-class>
        <init-param>
            <param-name>time</param-name>
            <param-value>3600</param-value>
        </init-param>
        <init-param>
            <param-name>scope</param-name>
            <param-value>application</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>oscache</filter-name>
        <url-pattern>/items/*</url-pattern>
    </filter-mapping>


#### oscache 标签使用
    <%@ taglib prefix="oscache" uri="http://www.opensymphony.com/oscache" %>  

    现在时间:<%=new Date()%>
    <br/>
    <oscache:cache >
      缓存时间:<%= new Date()%>
    </oscache:cache>
    
    
    <p>session</p>
    现在时间:<%=new Date()%>
    <br/>
    <oscache:cache scope="session">
      缓存时间:<%= new Date()%>
    </oscache:cache>
    
    
    <p>key</p>
    现在时间:<%=new Date()%>
    <br/>
    <oscache:cache key="cace">
      缓存时间:<%= new Date()%>
    </oscache:cache>
    
    
    现在时间:<%=new Date()%>
    <br/>
    <oscache:cache  time="4">
      缓存时间:<%= new Date()%>
    </oscache:cache> 

#### 缓存对象
    public class BaseCache extends GeneralCacheAdministrator {
    
        //过期时间单位(秒)
        private int refreshPeriod;
        //key 缓存前缀
        private String keyPrefix;
        
        
        public BaseCache(String keyPrefix,int refreshPeriod) {
            super();
            this.keyPrefix = keyPrefix;
            this.refreshPeriod = refreshPeriod;
        }
    
        public void put(String key,Object value) {
            putInCache(keyPrefix+"_"+key,value);
        }


        //expiredTime 单位秒
        public void put(String key,Object value,int expiredTime) {
            putInCache(keyPrefix+"_"+key,value,new ExpiresRefreshPolicy(expiredTime));
        }
    
        public void remove(String key){
            flushEntry(keyPrefix+"_"+key);
        }
    
        public void removeAll() {
            flushAll();
        }
    
        public Object get(String key) throws Exception{
            try {
                return getFromCache(keyPrefix+"_"+key,refreshPeriod);
            } catch (NeedsRefreshException e){
                cancelUpdate(keyPrefix+"_"+key);
                throw e;
            }
        }

        public Object getExpired(String key) throws Exception {
            try {
                return getFromCache(keyPrefix+"_"+key);
            } catch (NeedsRefreshException e){
                cancelUpdate(keyPrefix+"_"+key);
                throw e;
            }
    
        }


    }
------------------------------------------------
    public class CacheManager {
        private BaseCache baseCache;
    
        private static CacheManager instance;
        private static Object lock = new Object();
    
        private CacheManager () {
            //缓存20秒
            baseCache = new BaseCache("cache",20);
        }

        public static CacheManager getInstance() {
            if(instance == null) {
                synchronized (lock){
                    if(instance == null) {
                        instance = new CacheManager();
                    }
                }
            }
    
            return instance;
        }

        public BaseCache getBaseCache() {
            return baseCache;
        }
    
    }

---------------------------------------
       @Override
    public User getUserById(int id)  {
        User user = null;
        try {
            user = (User) CacheManager.getInstance().getBaseCache().get("id:"+id);
        } catch (Exception e) {
            e.printStackTrace();
        }

        if(user == null) {
            user = userMapper.findUserById(id);
            CacheManager.getInstance().getBaseCache().put("id:"+id,user);
        } else{
        }

        return  user;
    }

## 日期类型转换
#### 局部转换
    @RequestMapping("submit")
    public String submit(String name, Date birthday) {
        System.out.println();
        return "xxx.jsp";
    }

    @InitBinder
    public void initBinder(WebDataBinder binder, WebRequest request) {
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        binder.registerCustomEditor(Date.class,new CustomDateEditor(dateFormat,true));

        SimpleDateFormat dateFormat2 = new SimpleDateFormat("yyyy-MM-dd");
        binder.registerCustomEditor(Date.class,new CustomDateEditor(dateFormat2,true));
    }

* 使用InitBinder注解方法
* WebDataBinder 通过registerCustomEditor来注册要转换的类型

####整体转换
##### 自定义时间转换器
    /**
     * Created by lihao on 17/2/17.
     * 日期自定义转换器
     */
    public class BindingInitializer implements WebBindingInitializer {
        
        @Override
        public void initBinder(WebDataBinder binder, WebRequest request) {
            //可以写多种日期格式
    
            //格式1
            DateFormat dateFormat1 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
            binder.registerCustomEditor(Date.class,new CustomDateEditor(    dateFormat1,true));
    
            //格式2
            DateFormat dateFormat2 = new SimpleDateFormat("yyyy-MM-dd ");
            binder.registerCustomEditor(Date.class,new CustomDateEditor(    dateFormat2,true));
    
            //格式3
            DateFormat dateFormat3 = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
            binder.registerCustomEditor(Date.class,new CustomDateEditor(    dateFormat3,true));
    
            //格式4
            DateFormat dateFormat4 = new SimpleDateFormat("yyyy/MM/dd");
            binder.registerCustomEditor(Date.class,new CustomDateEditor(    dateFormat4,true));
    
        }
    }

##### 配置spring 
  <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
        <!--配置全局日期转换器-->
        <property name="webBindingInitializer">
            <bean class="com.lihao.shangcheng.web.BindingInitializer"/>
        </property>
    </bean>
























--------------------------------
2017.2.10