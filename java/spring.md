 ## ssh
* struts：web层，ValueStack值栈，拦截器
* hibernate mydatis： dao层
* spring： service层


![spring.png](http://upload-images.jianshu.io/upload_images/1197747-104cd86a156109e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## spring 框架
#### 什么是spring
	Spring是一个开源框架，Spring是于2003 年兴起的一个轻量级的Java 开发框架,框架的主要优势之一就是其分层架构，分层架构允许使用者选择使用哪一个组件，同时为 J2EE 应用程序开发提供集成的框架。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益。Spring的核心是控制反转（IoC）和面向切面（AOP）。简单来说，Spring是一个分层的JavaSE/EE full-stack(一站式) 轻量级开源框架。
	轻量级：与EJB对比，依赖资源少，销毁的资源少。
	分层： 一站式，每一个层都提供的解决方案
	web层：struts，spring-MVC
	service层：spring
	dao层：hibernate，mybatis ， jdbcTemplate  --> spring-data

#### spring核心
	Spring的核心是控制反转（IoC）和面向切面（AOP）

#### spring 优点
* 方便解耦，简化开发（高内聚低耦合）
	* Spring就是一个大工厂（容器），可以将所有对象创建和依赖关系维护，交给Spring管理	
	* spring工厂是用于生成bean

* AOP编程的支持	
	Spring提供面向切面编程，可以方便的实现对程序进行权限拦截、运行监控等功能

* 声明式事务的支持	
	只需要通过配置就可以完成对事务的管理，而无需手动编程

* 方便程序的测试	
	Spring对Junit4支持，可以通过注解方便的测试Spring程序

* 方便集成各种优秀框架
	Spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如：Struts、Hibernate、MyBatis、Quartz等）的直接支持

* 降低JavaEE API的使用难度
	* Spring 对JavaEE开发中非常难用的一些API（JDBC、JavaMail、远程调用等），都提供了封装，使这些API应用难度大大降低	

#### spring 体系架构

![spring2.png](http://upload-images.jianshu.io/upload_images/1197747-57398e0a7077f052.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 核心容器：beans,core,context,expression

#### IOC控制反转 使用
##### IOC 解释
	IoC Inverse of Control 反转控制的概念，就是将原本在程序中手动创建HelloService对象的控制权，交由Spring框架管理，简单说，就是创建HelloService对象控制权被反转到了Spring框架

##### 导入jar 包
	4+1  4个核心（beans、core、context、expression） +1个依赖（conmmons.loging）

* spring-beans-3.2.0.RELEASE.jar
	* 所有应用都要用到的，它包含访问配置文件、创建和管理bean
	* 以及进行Inversion of Control(IoC) / Dependency Injection(DI)操作相关的所有类
* spring-context-3.2.0.RELEASE.jar
	* Spring提供在基础IoC功能上的扩展服务，此外还提供许多企业级服务的支持
	* 如邮件服务、任务调度、JNDI定位、EJB集成、远程访问、缓存以及各种视图层框架的封装等
* spring-core-3.2.0.RELEASE.jar
	包含Spring框架基本的核心工具类，Spring其它组件要都要使用到这个包里的类,是其它组件的基本核心

* spring-expression-3.2.0.RELEASE.jar
	Spring表达式语言
* com.springsource.org.apache.commons.logging-1.1.1.jar
	第三方的主要用于处理日志

##### 添加配置文件
* 位置：任意，开发中一般在classpath下（src）
* 名称：任意，开发中常用applicationContext.xml
* 内容：添加schema约束
###### demo
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans 
	       					   http://www.springframework.org/schema/beans/spring-beans.xsd">
		<!-- 配置service 
			<bean> 配置需要创建的对象
				id ：用于之后从spring容器获得实例时使用的
				class ：需要创建实例的全限定类名
		-->
		<bean id="userServiceId" class="cn.leyue.service.impl.UserServiceImp"></bean>
	</beans>

	使用

	String xmlPath = "cn/leyue/beans.xml";
	ClassPathXmlApplicationContext application = new ClassPathXmlApplicationContext(
			xmlPath);
	UserService userservice = (UserService) application.getBean("userServiceId");
	userservice.adduser();

##### DI解释
	DI：Dependency Injection 依赖注入，在Spring框架负责创建Bean对象时，动态的将依赖对象注入到Bean组件	

##### 例
	  <bean id="userServiceId2" class="cn.leyue.ioc.UserServiceImpl">
        <property name="userDao" ref="userdaoId"></property>
    </bean>	


    String xmlPath = "cn/leyue/ioc/beans.xml";
    ClassPathXmlApplicationContext application = new ClassPathXmlApplicationContext(xmlPath);
    UserService service = (UserService) application.getBean("userServiceId2");
    service.saveUser();


    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

## 装配Bean 基于xml
#####   解决spring的java.lang.IllegalArgumentException异常。3x 和1.8 不兼容  升级到4.x
### 实例化方式
* 默认构造
* 静态工厂
* 实例工厂
#### 默认构造
	<bean id="" class="">  必须提供默认构造
#### 静态方法
* 常用于spring 整合其他框架
* 静态工厂：用于生成实例对象，所有的方法必须是static
	 <bean id="userserviceId" class="cn.leyue.inject.a_static.MyStaticFactory" factory-method="getService">
	 </bean>

	 public static UserService getService() {
        return new UserServiceImpl();
    }

#### 实例工厂
	实例工厂：必须先有工厂实例对象，通过实例对象创建对象，提供所有的方法都是“非静态”。
	factory-bean 确定实例工厂实例
	factory-method 确定普通方法
	    <bean id="myfactoryId" class="cn.leyue.inject.MyFactory"></bean>
    <bean id="userServiceId" factory-bean="myfactoryId" factory-method="getService"></bean>  

    public class MyFactory {

	    public  UserService getService() {
	        return new UserServiceImpl();
	    }
	}  


## 声明周期
####  初始化和销毁
	目标方法执行前 执行后，将进行初始化或销毁。
	<bean id="" class="" init-method="初始化方法名称"  destroy-method="销毁的方法名称">
##### 目标类
	public class MyObject {
	    public void show() {
	        System.out.println("我是Objectl");
	    }

	    public void init(){
	        System.out.println("------init()");
	    }

	    public void desdoryed() {
	        System.out.println("------desdoryed()");
	    }
	}
##### spring配置
	   <!--init-method 用于配置初始化方法,准备数据等-->
    <!--destroy-method 用于配置销毁方法,清理资源等-->

    <bean id="objectId" class="cn.leyue.a_cycle.MyObject"
          init-method="init" destroy-method="desdoryed"
    >

    </bean>

##### 测试
		String path = "cn/leyue/a_cycle/beans.xml";
        ClassPathXmlApplicationContext application = new ClassPathXmlApplicationContext(path);
        MyObject myObject = application.getBean("objectId", MyObject.class);
        myObject.show();
        //执行destroy 1,必须容器close 2,必须是单例
        application.close();    

## 构造方法
##### 目标类
	public class User {
	    private int age;
	    private int uid;
	    private String name;

	    public User(int age, int uid) {
	        this.age = age;
	        this.uid = uid;
	    }

	    public User(int uid, String name) {
	        this.uid = uid;
	        this.name = name;
	    }

	    @Override
	    public String toString() {
	        return "User{" +
	                "age=" + age +
	                ", uid=" + uid +
	                ", name='" + name + '\'' +
	                '}';
	    }
	}

##### spring 配置
	<constructor-arg> 用于配置构造方法一个参数argument
	name ：参数的名称
	value：设置普通数据
	ref：引用数据，一般是另一个bean id值
	
	index ：参数的索引号，从0开始 。如果只有索引，匹配到了多个构造方法时，默认使用第一个。
	type ：确定参数类型


	 <bean id="userId" class="cn.leyue.constructor.User">
      <constructor-arg index="0" type="int" value="12"></constructor-arg>
      <constructor-arg index="1" type="int" value="90"></constructor-arg>
    </bean>

## setter 方法
#####  目标类
	public class Person {
	    public String pname;
	    public int age;
	    public Address homeAddr;
	    public Address companyAddr;

	    public String getPname() {
	        return pname;
	    }

	    public void setPname(String pname) {
	        this.pname = pname;
	    }

	    public int getAge() {
	        return age;
	    }

	    public void setAge(int age) {
	        this.age = age;
	    }

	    public Address getHomeAddr() {
	        return homeAddr;
	    }

	    public void setHomeAddr(Address homeAddr) {
	        this.homeAddr = homeAddr;
	    }

	    public Address getCompanyAddr() {
	        return companyAddr;
	    }

	    public void setCompanyAddr(Address companyAddr) {
	        this.companyAddr = companyAddr;
	    }

	    @Override
	    public String toString() {
	        return "Person{" +
	                "pname='" + pname + '\'' +
	                ", age=" + age +
	                ", homeAddr=" + homeAddr +
	                ", companyAddr=" + companyAddr +
	                '}';
	    }
	}


##### spring 配置
	setter方法注入 
		* 普通数据 
			<property name="" value="值">
			等效
			<property name="">
				<value>值
		* 引用数据
			<property name="" ref="另一个bean">
			等效
			<property name="">
				<ref bean="另一个bean"/>

	 <bean id="homeaddr" class="cn.leyue.setter.Address">
	        <property name="addr" value="北京"></property>
	        <property name="tel" value="138"></property>
	    </bean>
	    <bean id="companaddr" class="cn.leyue.setter.Address">
	        <property name="addr" value="北土城"></property>
	        <property name="tel" value="168"></property>
	    </bean>
	    <bean id="person" class="cn.leyue.setter.Person">
	        <property name="pname" value="小猪"></property>
	        <property name="age" value="22"></property>
	        <property name="companyAddr" ref="homeaddr"></property>
	        <property name="homeAddr" ref="companaddr"></property>

    </bean>

## P命令空间    
* 对“setter方法注入”进行简化，替换<property name="属性名">，而是在
	<bean p:属性名="普通值"  p:属性名-ref="引用值">
* p命名空间使用前提，必须添加命名空间
	xmlns:p ="http://www.springframework.org/schema/p"

    <bean id="person2" class="cn.leyue.setter.Person"
      p:pname="小王"
      p:age="45"
      p:homeAddr-ref="homeaddr"
      p:companyAddr-ref="companaddr"  >
      </bean>


## 集合注入
	集合的注入都是给<property>添加子标签
		数组：<array>
		List：<list>
		Set：<set>
		Map：<map> ，map存放k/v 键值对，使用<entry>描述
		Properties：<props>  <prop key=""></prop>  【】
		
	普通数据：<value>
	引用数据：<ref>

##### spring 配置 
	<bean id="collDataId" class="cn.leyue.coll.CollData">
	      <property name="arrayData">
	          <value>arrayData</value>

	      </property>
	      <property name="listData">
	          <list>
	              <value>listData</value>
	          </list>

	      </property>
	      <property name="mapData">
	          <map>
	              <entry key="jack" value="杰克"></entry>
	          </map>
	      </property>
	      <property name="setData">
	          <set>
	              <value>set1</value>
	          </set>
	      </property>
	      <property name="propsFata">
	          <props>
	              <prop key="props">pro</prop>
	          </props>
	      </property>
	  </bean>


#####  目标类
	public class CollData {
	    private String[] arrayData;
	    private List<String> listData;
	    private Set<String> setData;
	    private Map<String,String> mapData;
	    private Properties propsFata;

	    public String[] getArrayData() {
	        return arrayData;
	    }

	    public void setArrayData(String[] arrayData) {
	        this.arrayData = arrayData;
	    }

	    public List<String> getListData() {
	        return listData;
	    }

	    public void setListData(List<String> listData) {
	        this.listData = listData;
	    }

	    public Set<String> getSetData() {
	        return setData;
	    }

	    public void setSetData(Set<String> setData) {
	        this.setData = setData;
	    }

	    public Map<String, String> getMapData() {
	        return mapData;
	    }

	    public void setMapData(Map<String, String> mapData) {
	        this.mapData = mapData;
	    }

	    public Properties getPropsFata() {
	        return propsFata;
	    }

	    public void setPropsFata(Properties propsFata) {
	        this.propsFata = propsFata;
	    }

	    @Override
	    public String toString() {
	        return "CollData{" +
	                "arrayData=" + Arrays.toString(arrayData) +
	                ", listData=" + listData +
	                ", setData=" + setData +
	                ", mapData=" + mapData +
	                ", propsFata=" + propsFata +
	                '}';
	    }
	}


## 装配Bean 基于注解
* 注解：就是一个类，使用@注解名称
* 开发中：使用注解取代xml配置

#### @Component 取代<bean class="">
	@Compnent("id") 取代<bean id="" class="">
#### web开发,提供3个@Component注解衍生注解取代	<bean class="">
##### @Repository : dao层
##### @Service: service层
##### @Controller: web层

#### 依赖注入 给私有字段设置，也可以给setter方法设置。
##### 普通值： @Value("")
##### 引用值
* 方式1： 按照【类型】注入
	@Autowired
* 方式2：按照【名称】注入
	@Autowried
	@Qualifier("名称")
* 方式3：按照【"名称"】
	@Resource("名称")

#### 生命周期
* 初始化：@PostConstruct
* 销毁：@PreDestroy

#### 作用域
	@Scope("prototype") 多例
###### 示例
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/context
	         http://www.springframework.org/schema/context/spring-context.xsd
	        "
	>
	    <context:component-scan base-package="cn.leyue.annotation"/>
	    <!--<bean id="ActionId" class="cn.leyue.annotation.StudentAction"></bean>-->

	</beans>
	

----------------------------------------------------
	@Controller("ActionId")
	public class StudentAction {

	    @Autowired
	    public StudentService service;
	    public void execute(){
	//        System.out.println("execute");
	        service.addUser();
	    }

	    @PostConstruct
	    public void init() {
	        System.out.println("初始化");
	    }

	    @PreDestroy
	    public void desdoryed(){
	        System.out.println("销毁");
	    }
	}
------------------------------------------------------------------------
	public interface StudentService {
	     void addUser() ;
	}
------------------------------------------------------------------------
	@Service
	public class StudentServiceImpl implements StudentService {

	    @Autowired
	    @Qualifier("dao")
	    private StudentDao studentDao;
	    @Override
	    public void addUser() {
	//        System.out.println("addUser");
	        studentDao.save();
	    }
	}

------------------------------------------------------------------------
	public interface StudentDao {
	    void save();
	}	
------------------------------------------------------------------------
	@Repository("dao")
	public class StudentDaoImpl implements StudentDao {
	    @Override
	    public void save() {
	        System.out.println("save");
	    }
	}
----------------------------------------------------
	public class TestWeb {

    @Test
    public void testweb() {
        String path = "cn/leyue/annotation/beans.xml";
        ClassPathXmlApplicationContext application = new ClassPathXmlApplicationContext(path);
        StudentAction action = application.getBean("ActionId",StudentAction.class);
        action.execute();
        application.close();
    }
}







## 遇到的问题
#### 没有添加 spring-aop-4.1.6.RELEASE.jar 导致
	org.springframework.beans.factory.BeanDefinitionStoreException: Unexpected exception parsing XML document from class path resource [cn/leyue/annotation/beans.xml]; nested exception is java.lang.NoClassDefFoundError: org/springframework/aop/TargetSource



------------------------------------
2016.11.27