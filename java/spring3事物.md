## 数据库事务
	数据库事务(Database Transaction) ，是指作为单个逻辑工作单元执行的一系列操作，
	要么完全地执行，要么完全地不执行。
	一个逻辑工作单元要成为事务，必须满足所谓的ACID（原子性、一致性、隔离性和持久性）属性。

##### 原子性	（Atomic）
	事务必须是原子工作单元；对于其数据修改，要么全都执行,要么全都不执行。（整体）

##### 一致性（Consistent）
	事务在完成时，必须使所有的数据都保持一致状态。（完成）

##### 隔离性（Insulation）
	由并发事务所做的修改必须与任何其它并发事务所做的修改隔离。（并发）

##### 持久性（Duration）
	事务完成以后，它对于系统的影响是永久性的。该修改即使出现致命的系统故障已将一直保持。（结果）


### 隔离问题
##### 脏读
	一个事务读到另一个事务没有提交的数据。

##### 不可重复读
	一个事务读取到另一个事务已提交的数据（update）。

##### 虚度（幻读）	
	一个事务读取到另一个事务已提交的数据（insert）。

### 隔离级别
* read uncommitted: 读未提交。存在以上3个问题。
* read committed:读已提交。解决脏读，存在2个问题。
* repeatable read: 可重复读。解决：脏读，不可重复读，存在1个问题。
* serializable: 串行化。都解决，单事务。


##### mysql事物操作
	Connection conn = null;
    try{
        //1 数据库链接
        conn = ...
        //2 开启事物
        conn.setAutoCommit(false);
        操作A
        操作B
        操作C
        操作D
        
        //3 提交事务
        conn.commit();
        
    } catche() {
        //4 回滚事务
        conn.rollback();
    }

##### mysql事务操作保存点（Savepoint）
	Connection conn = null;
    //保存点,记录操作的当前位置,之后可以回滚到指定的位置.(可以回滚一部分)
    Savepoint savepoint = null;
    try{
        //1 数据库链接
        conn = ...
        //2 开启事物
        conn.setAutoCommit(false);
        操作A
        操作B
        savepoint = conn.setSavepoint();
        操作C
        操作D
        //3 提交事务
        conn.commit();
    } catche() {
        //4 回滚事务
        if(savepoint != null) { // CD异常
            //回滚到CD之前
            conn.rollback(savepoint)
            //提交AB
            conn.commit();
        } else {
            //回滚AB
            conn.rollback();
        }
    }


## spring 事物管理介绍
#### 添加jar包 spring-tx-4.3.5.RELEASE.jar。
* PlatformTranscationManager 平台事物管理器，spring 要管理事务，必须使用事物管理器。
	进行事务配置时，必须配置事物管理器。
* TransactionDefinition：事物详情（事务定义，事务属性），spring用于确定事物具体详情，例如
	隔离级别，是否读取，超时等。
	进行事物配置时，必须配置详情。spring 将配置项封装到该对象实例。
* TransactionStatus: 事务状态，spring用于记录当前事务运行状态。
	是否有保存点，事务是否完成。

#### PlatformTransactionManager 事务管理器
##### 导入jar包：需要平台事物管理器的实现类。
* spring-jdbc-4.3.5.RELEASE.jar  jdbc开发
* spring-orm-4.3.5.RELEASE  	 整合hibernate

##### 常见的事物管理器
DataSourceTransactionManager   jdbc开发时事物管理器，采用JdbcTemplate。
HibernateTransactionManager    hibernate开发时事物管理器，整合hibernate。
* api方法
 TransactionStatus getTransaction(TransactionDefinition var1) 事务管理器 通过“事务详情”，获得“事物状态”，从而管理事务。

void commit(TransactionStatus status)  根据状态提交
void rollback(TransactionStatus status) 根据状态回滚

#### TransactionStatus 事务状态
* boolean isNewTransaction();   是否是新事物
* boolean hasSavepoint();       是否有保存点
* void setRollbackOnly();		设置回滚
* boolean isRollbackOnly();		是否回滚
* void flush();					刷新
* boolean isCompleted();		是否完成

#### TransactionDefinition 事物详情（事务定义，事务属性）
	//传播行为
	int PROPAGATION_REQUIRED = 0;
    int PROPAGATION_SUPPORTS = 1;
    int PROPAGATION_MANDATORY = 2;
    int PROPAGATION_REQUIRES_NEW = 3;
    int PROPAGATION_NOT_SUPPORTED = 4;
    int PROPAGATION_NEVER = 5;
    int PROPAGATION_NESTED = 6;
----------------------------------------
	//隔离级别
	int ISOLATION_DEFAULT = -1;
    int ISOLATION_READ_UNCOMMITTED = 1;
    int ISOLATION_READ_COMMITTED = 2;
    int ISOLATION_REPEATABLE_READ = 4;
    int ISOLATION_SERIALIZABLE = 8;
----------------------------------------
	//默认超时时间。默认值 -1.使用数据库底层的超时时间
    int TIMEOUT_DEFAULT = -1;
----------------------------------------
	int getPropagationBehavior();  传播行为
	int getIsolationLevel();       隔离级别
    int getTimeout();			获得超时时间
    boolean isReadOnly();	是否只读（增删改：读写； 查询：只读）
    String getName();		配置事务详情名称。一般方法名称。例如：save,add* 等。

* 传播行为：在两个业务之间如何共享事务。
	* PROPAGATION_REQUIRED , required , 必须  【默认值】
		支持当前事务，A如果有事务，B将使用该事务。
		如果A没有事务，B将创建一个新的事务。
	* PROPAGATION_SUPPORTS ，supports ，支持
	 		支持当前事务，A如果有事务，B将使用该事务。
	 		如果A没有事务，B将以非事务执行。
	* PROPAGATION_MANDATORY，mandatory ，强制
	 		支持当前事务，A如果有事务，B将使用该事务。
	 		如果A没有事务，B将抛异常。
	* PROPAGATION_REQUIRES_NEW ， requires_new ，必须新的
	 		如果A有事务，将A的事务挂起，B创建一个新的事务
	 		如果A没有事务，B创建一个新的事务
	* PROPAGATION_NOT_SUPPORTED ，not_supported ,不支持
	 		如果A有事务，将A的事务挂起，B将以非事务执行
	 		如果A没有事务，B将以非事务执行
	* PROPAGATION_NEVER ，never，从不
	 		如果A有事务，B将抛异常
	 		如果A没有事务，B将以非事务执行
	* PROPAGATION_NESTED ，nested ，嵌套
		A和B底层采用保存点机制，形成嵌套事务。

* 经常使用
PROPAGATION_REQUIRED   PROPAGATION_REQUIRES_NEW  PROPAGATION_NESTED

#### 事务处理 AOP配置基于xml
* 使用配置 在spring xml 配置aop自动生成代理，进行事物的管理
	* 1，配置管理器。
	* 2，配置事物详情。
	* 3，配置aop.
##### xml 配置 applicationContext-tx.xml 
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                            http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context
                            http://www.springframework.org/schema/context/spring-context.xsd
                            http://www.springframework.org/schema/aop
                            http://www.springframework.org/schema/aop/spring-aop.xsd
                             http://www.springframework.org/schema/tx
       					   http://www.springframework.org/schema/tx/spring-tx.xsd"
>
    <!--组件扫描-->
    <context:component-scan base-package="com.springdemo"></context:component-scan>

    <bean id="dataSourceId" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl"
                  value="jdbc:mysql://localhost:3306/school?useUnicode=true&amp;characterEncoding=UTF-8"></property>
        <property name="user" value="root"></property>
        <property name="password" value=""></property>

    </bean>
    <bean id="template" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSourceId"></property>
    </bean>

    <bean id="accountDaoId" class="com.springdemo.dao.impl.AccountDaoImpl">
        <property name="jdbcTemplate" ref="template"></property>
    </bean>
    <bean id="serviceId" class="com.springdemo.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDaoId"></property>
    </bean>

    <!--事务管理-->
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSourceId"></property>
    </bean>

    <!--
    事务详情
            <tx:attributes> 用于配置事务详情（属性属性）
			<tx:method name=""/> 详情具体配置
				propagation 传播行为 ， REQUIRED：必须；REQUIRES_NEW:必须是新的
				isolation 隔离级别
    -->
    <tx:advice id="txAdvice" transaction-manager="txManager">
        <tx:attributes>
            <!--事务名称-->
            <tx:method name="transfer" propagation="REQUIRED" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>
    <!--AOP编程，目标类有ABCD（4个连接点），切入点表达式-->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.springdemo.service.impl.AccountServiceImpl.*(..))"></aop:advisor>
    </aop:config>

	</beans>














































