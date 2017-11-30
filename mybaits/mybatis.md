## Mapper

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

