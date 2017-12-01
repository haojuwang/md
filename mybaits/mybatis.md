##Mapper

```
namespace  对应的java mapper
```

```
reusltMap  bean的对应关系
```

```
select: 表示一个MappedStatement对象
```

```
id: 表示 java Mapper对应的方法名称
```

```
#{} 表示一个占位符 ？
#{id}: 里面的id表示输入参数的参数名称，如果该参数是简单类型，那么#{}里面的参数名称可以任意

${}: 表示一个sql的连接符。
${value}：里面的value 表示参数的参数名称，如果该参数是简单类型，那么${}里面的参数名称必须是value。
${}这种写法存在sql 注入的风险，所以要慎用，但是在一些场景下必须使用${},比如排序时，动态传入排序的列名，${}会原样输出 不加解析

select * from user where name = #{name};

select * from user where name = '${name}';


#{ } 解析为一个 JDBC 预编译语句（prepared statement）的参数标记符。
select * from user where name = #{name};
select * from user where name = ?;
一个 #{ } 被解析为一个参数占位符 ? 。


${ } 仅仅为一个纯碎的 string 替换，在动态 SQL 解析阶段将会进行变量替换
select * from user where name = '${name}';
select * from user where name = "ruhua";


https://segmentfault.com/a/1190000004617028

```

```
paramterType:输入参数的java类型
resultType: 输出结果的所映射的java 类型
```

```xml
<mapper namespace="com.oa.auth.mapper.UserMapper">

    <select id="findByName"  parameterType="java.lang.String" resultType="com.oa.auth.model.User">
        SELECT * FROM user WHERE username LIKE '%${value}%'
    </select>

    <select id="allListByOrder" parameterType="String" resultType="com.oa.auth.model.User">
        SELECT * FROM USER ORDER BY ${value} DESC
    </select>

</mapper>
```









AuthorizationMapper.java

```Java
public interface AuthorizationMapper {
    void authorize(Authorization authorization);

    void unauthorize(long id);

    Authorization findById(long id);
}

```



AuthorizationMapper.xml

```Xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">  

<mapper namespace="com.oa.auth.mapper.AuthorizationMapper">
    <resultMap id="AuthorizationResultMap" type="com.oa.auth.model.Authorization">
        <result  column="id" property="id"></result>
        <result column="employee_id" property="employeeId"></result>
        <result column="role_id" property="roleId"></result>
    </resultMap>

    <insert id="authorize" parameterType="com.oa.auth.model.Authorization" useGeneratedKeys="true" keyProperty="id">
       INSERT INTO authorization(employee_id,role_id) VALUES(#{employeeId},#{roleId})
    </insert>

    <delete id="unauthorize" parameterType="long">
        DELETE FROM authorization WHERE id=#{id}

    </delete>

    <select id="findById" parameterType="long" resultMap="AuthorizationResultMap">
        SELECT * FROM authorization WHERE id=#{id}
    </select>

</mapper>
```



spring 配置

```Xml
 <!--jdbc 配置文件-->
    <bean id="propertyPlaceholderConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath*:jdbc.properties</value>
            </list>
        </property>
    </bean>

    <!--配置数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>


    <!--配置mybatis sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="mapperLocations" value="classpath*:com/oa/auth/mapper/**/*.xml"></property>
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    </bean>


    <!--自动扫描mapper接口文件-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.oa.auth.mapper"></property>
    </bean>

```



#### select

```XML
<select
	id="selectPerson"
	parameterType="int"
	resultType="hashmap"
	resultMap="personResultMap"
	flushCache="false"
	useCache="true"
	timeout="10000"
	fetchSize="256"
	statementType="PREPARED"
	resultSetType="FORWARD_ONLY"
>
```

|      属性       |                    描述                    |
| :-----------: | :--------------------------------------: |
|      id       |         在命名空间中唯一的标识符，可以被用来引用这条语句         |
| parameterType |          将会传入这条语句的参数类的完全限定名或别名           |
|   resultTpe   | 从这条语句中返回的期望类型的类的完全限定名或别名。注意集合情形，那应该是集合可以包含的类型，而不能是集合本身。使用resultType或resultMap,但不能同时使用。 |
|   resultMap   | 命名引用外部的resultMap。返回map是MyBatis最具有力量的特性，对其有一个很好的理解的话，许多复杂映射的情形就能被解决了。使用resultMap或resultTpe，但不能同时使用。 |
|  flushCache   | 将其设置为true,无论语句什么时候被调用，都会导致缓存被清空。默认值：false |
|   useCache    |    将其设置为true,将会导致本条语句的结果被缓存，默认值：true     |
|    timeout    | 这个设置驱动程序等待数据库返回请求结果，并抛出异常时间的最大等待值。默认不设置（驱动自行处理） |
| statementType | STATEMENT,PREPARED 或 CALLABLE 的一种。这会让 MyBatis使用选择使用 Statement,PreparedStatement 或 CallableStatement。默认值:PREPARED。 |
| resultSetType | FORWARD_ONLY\|SCROLL_SENSITIVE\|SCROLL_INSENSITIVE中的一种。默认是不设置(驱动自行处理)。 |
|               |                                          |
|               |                                          |



#### insert,update,delete

```xml
<insert
	id="insertAuthor"
	parameterType="domain.blog.Author"
	flushCache="true"
	statementType="PREPARED"
	keyProperty=""
	useGenerateKeys=""
	timeout="2000"
>

<update
	id="insertAuthor"
	parameterType="domain.blog.Author"
	flushCache="true"
	statementType="PREPARED"
	timeout="2000"
>

<delete
	id="insertAuthor"
	parameterType="domain.blog.Author"
	flushCache="true"
	statementType="PREPARED"
	timeout="20000"
>
```



|       属性        |                    描述                    |
| :-------------: | :--------------------------------------: |
|       id        |         在命名空间中唯一的标识符，可以被用来引用这条语句         |
|  parameterType  |          将会传入这条语句的参数类的完全限定名或别名           |
|   flushCache    | 将其设置为true,无论语句什么时候被调用，都会导致缓存被清空。默认值：false |
|     timeout     | 这个设置驱动程序等待数据库返回请求结果，并抛出异常时间的最大等待值。默认不设置（驱动自行处理） |
|  statementType  | STATEMENT,PREPARED 或 CALLABLE 的一种。这会让 MyBatis使用选择使用 Statement,PreparedStatement 或 CallableStatement。默认值:PREPARED。 |
| useGenerateKeys | (仅对insert有用)这会告诉MyBatis 使用JDBC的getGenratedKeys方法来取出由数据（比如：像mysql和sql server这样的数据库管理系统的自动递增字段）内部生成的主键。默认值：false |
|   keyProperty   | (仅对insert有用)标记一个属性，Mybatis会通过getGeneratedKeys或者通过insert语句的selectKey子元素设置它的值。默认：不设置 |



```xml
<insert id="insertAuthor" parameterType="domain.blog.Author" useGeneratedKeys=”true” keyProperty=”id”>

insert into Author (username,password,email,bio) values (#{username},#{password},#{email},#{bio})

</insert>
```





#### selectKey

```xml
<selectKey
	keyProperty="id"
	reusltType="int"
	order="BEFORE"
	statementType="PREPARED"
>
```



|      属性       |                    描述                    |
| :-----------: | :--------------------------------------: |
|  keyProperty  |         selectKey语句结果应该被设置的目标属性          |
|  resultType   | 结果的类型。Mybatis 通常可以算出，但是写上也没有问题。Mybatis 允许任务简单类型用作主键的类型，包括字符串。 |
|     order     | 这可以被设置为BEFORE或AFTER。如果设置为BEFORE，那么他会首先选择主键，设置keyProperty然后执行插入语句。如果设置为AFTER，那么先执行插入语句，然后是selectKey元素。 |
| statementType | STATEMENT,PREPARED 或 CALLABLE 的一种。这会让 MyBatis使用选择使用 Statement,PreparedStatement 或 CallableStatement。默认值:PREPARED。 |

















#### 自增主键

````xml
   <insert id="insertUser" parameterType="com.oa.auth.model.User">
        <selectKey keyProperty="id" resultType="int" order="AFTER">
            SELECT LAST_INSERT_ID()
        </selectKey>
        INSERT INTO USER(username,birthday,sex,address)
        VALUES (#{username},#{birthday},#{sex},#{address})
    </insert>
````

* selectKey: 查询主键，在标签内需要输入查询主键的sql.
* order: 指定查询主键的sql和insert语句的执行顺序，相对于insert语句来说.
* LAST_INSERT_ID： 该函数是mysql的函数，获取自增主键的ID,它必须配合insert语句一起使用.



#### 主键uuid

```xml
 <insert id="insertUser2" parameterType="com.oa.auth.model.User">
        <selectKey keyProperty="random" resultType="String" order="BEFORE">
            SELECT  uuid_short()
        </selectKey>
        INSERT INTO USER(username,birthday,sex,address,random)
        VALUES (#{username},#{birthday},#{sex},#{address},#{random})
    </insert>
```

* order 必须是“BEFORE” 先生成再插入。







## 动态sql

* if
* choose(when,otherwise)
* trim(where ,set)
* foreach



#### if 

```xml
    <select id="search" parameterType="com.oa.auth.model.User" resultType="com.oa.auth.model.User">
        SELECT * FROM USER WHERE sex=1
        <if test="username !=null and username != ''">
            AND username LIKE '%${username}%'
        </if>

    </select>
```





#### choose, when, otherwise

* 有些时候，我们不想用到所有的条件语句，而只想从中择其一二。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。



```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```



#### where

```xml
  <select id="search2" parameterType="com.oa.auth.model.User" resultType="com.oa.auth.model.User">
        SELECT * FROM USER
        <where>
            <if test="username !=null and username != ''">
                AND username LIKE '%${username}%'
            </if>

            <if test="sex !=null and sex != ''">
                AND sex = #{sex}
            </if>
        </where>

    </select>
```



#### trim

```xml
   <trim prefix="WHERE" prefixOverrides="AND|OR">
            <if test="username !=null and username != ''">
                AND username LIKE '%${username}%'
            </if>

            <if test="sex !=null and sex != ''">
                AND sex = #{sex}
            </if>
        </trim>
```

*  prefixOverrides 属性会忽略通过管道分割的文本序列，它带来的结果就是所有在prefixOverriedes 属性中指定的内容被删除。







#### set 使用

* set 用于动态更新语句的解决访问。set 元素可以被用于动态包含需要更新的列，而舍去其他的。

```xml
   <update id="update" >
        UPDATE  USER
            <set>
                <if test="username != null">username=#{username},</if>
                <if test="birthday != null">birthday=#{birthday},</if>
                <if test="sex != null">sex=#{sex},</if>
                <if test="address != null">address=#{address},</if>
                <if test="random != null">random=#{random},</if>
            </set>

           WHERE id=#{id}
    </update>
```



#### sql 片段

* 定义可重用的SQL代码段，可以包含在其他语句中。

```xml
   <sql id="userColumns">id,username,password</sql>

    <select id="selectUsers" parameterType="int" resultType="hashmap">
        SELECT  <include refid="userColumns"/> FROM USER  WHERE id= #{id}
    </select>
```



















## 联表

#### resultMap 方式一对一

```xml
  <resultMap id="orderResult" type="com.oa.auth.model.Orders">
        <result column="user_id" property="userId"></result>
        <result column="number" property="number"></result>
        <result column="createtime" property="createtime"></result>
        <result column="note" property="note"></result>
        <association property="user" javaType="com.oa.auth.model.User">
            <id column="user_id" property="id"></id>
            <id column="username" property="username"></id>
            <id column="sex" property="sex"></id>
        </association>
    </resultMap>

    <select id="findOrdersAndUser"   resultMap="orderResult">

      SELECT
		orders.*,
		user.`username`,
		user.`sex`
		FROM
		orders,
		USER
		WHERE orders.`user_id` = user.`id`

    </select>

```

```java
public class Orders {
    private Integer id;

    private Integer userId;

    private String number;

    private Date createtime;

    private String note;

    private User user;
 }
```

* association: 一对一关联映射。
* property: 关联信息查询的结果将要映射的扩展类中的对象属性名称。
* javaType:  property 对应的java 类型。
* id 标签：建议在关联查询时必须写，不写不会报错，但是会影响性能。

 



#### association

* 嵌套查询：通过执行另外一个sql映射语句来返回预期的复杂类型。

* 嵌套结果：使用嵌套结果映射来处理重复的联合结果的子集。

  ​

  ​

| 属性       | 描述                                       |
| -------- | ---------------------------------------- |
| property | 映射到列结果的字段或属性。如果匹配的是存在的和给定名称相同的JavaBean的属性，那么就会使用。否则Mybatis将会寻找给定名称的字段。这两种情形你可以使用通常点式的复杂属性导航。比如，你可以这样映射一些东西；"username",或者映射到一些复杂的东西："address.street.number" |
| column   | 来自数据库的列名或重命名的列标签。这和通常传递给resultSet.getString(cloumnName)方法的字符串是相同的。注意:要处理复合主键,你可以指定多个列名通过 column=” {prop1=col1,prop2=col2}”这种语法来传递给嵌套查询语句。这会引起 prop1 和 prop2 以参数对象形式来设置给目标嵌套查询语句 |
| JavaType | 一个 Java 类的完全限定名,或一个类型别名(参加上面内建类型别名的列 表)。如果你映射到一个 JavaBean,MyBatis 通常可以断定类型。然而,如 果你映射到的是 HashMap,那么你应该明确地指定 javaType 来保证所需的 行为。 |
| jdbcType | 在这个表格之前的所支持的 JDBC 类型列表中的类型。JDBC 类型是仅仅 需要对插入,更新和删除操作可能为空的列进行处理。这是 JDBC 的需要, 而不是 MyBatis 的。如果你直接使用 JDBC 编程,你需要指定这个类型-但 仅仅对可能为空的值。 |
|          |                                          |
|          |                                          |















