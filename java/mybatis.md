##mybatis
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">


	<mapper namespace="com.springmvc.dao.UserMapper">


    <resultMap id="UserMap" type="com.springmvc.domain.User">
        <id property="id" column="id"/>
        <result property="username" column="username"></result>
        <result property="birthday" column="birthday"></result>
        <result property="sex" column="sex" ></result>
        <result property="address" column="address"></result>
    </resultMap>

    <select id="findUserById" parameterType="int" resultType="com.springmvc.domain.User">
        SELECT * FROM USER WHERE id=#{id}
    </select>
    <insert id="addUser" parameterType="com.springmvc.domain.User">
        INSERT INTO USER (username,birthday,sex,address)VALUES (#{username},#{birthday},#{sex},#{address})
    </insert>
    <select id="findAll"  resultType="com.springmvc.domain.User" >
        SELECT * FROM USER
    </select>

    <update id="updateUser" parameterType="com.springmvc.domain.User" >
        UPDATE  USER SET username=#{username} WHERE id=#{id}
    </update>

    <!--添加用户-->
    <insert id="insertUser" parameterType="com.mybaties.domain.User">
        <selectKey keyProperty="id" resultType="int" order="AFTER">
            SELECT LAST_INSERT_ID()
        </selectKey>
        INSERT  INTO USER (username,birthday,sex,address)
        VALUES (#{username},#{birthday},#{sex},#{address})
    </insert>

    <select id="groupbyUsername" resultType="map">
        SELECT username,COUNT(*) as count FROM USER GROUP BY username ORDER BY count DESC
    </select>
	</mapper>
## mapper
	<mapper namespace="com.springmvc.dao.UserMapper">
    	<resultMap id="UserMap" type="com.springmvc.domain.User">
    	    <id property="id" column="id"/>
    	    <result property="username" column="username"></result>
    	    <result property="birthday" column="birthday"></result>
    	    <result property="sex" column="sex" ></result>
    	    <result property="address" column="address"></result>
    	</resultMap>
	
    	<select id="findUserById" parameterType="int" 	resultType="com.springmvc.domain.User">
    	    SELECT * FROM USER WHERE id=#{id}
    	</select>
	</mapper>
* namespace 命名空间是对应的mapper的全限定名称。	
## resultMap bean属性与数据库字段对应关系
	<resultMap id="UserMap" type="com.springmvc.domain.User">
    	<id property="id" column="id"/>
    	<result property="username" column="username"></result>
    	<result property="birthday" column="birthday"></result>
    	<result property="sex" column="sex" ></result>
    	<result property="address" column="address"></result>
    </resultMap>
* 当Java bean 属性与数据库字段不一致时，需要一个对应关系，resultMap表示对应关系。
* id（唯一） 用于select标签 的resultMap属性值。
* type  java bean 全限定名称。
* property bean的属性名称。
* column 数据库字段名称。


##普通sql
### select 
    <select id="findUserById" parameterType="int" resultType="com.springmvc.domain.User">
        SELECT * FROM USER WHERE id=#{id}
    </select> 

* id 对应mapper 的方法。
* parameterType 输入参数类型。
* resultType最小对象粒度  这里返回的数据是List<User> 
	对应的resultType 是User。
* "#{id}" 表示占位符。

### update
	<update id="updateUser" parameterType="com.springmvc.domain.User" >
        UPDATE  USER SET username=#{username} WHERE id=#{id}
    </update>

#### 返回插入的id
	  <insert id="insertUser" parameterType="com.mybaties.domain.User">
        <selectKey keyProperty="id" resultType="int" order="AFTER">
            SELECT LAST_INSERT_ID()
        </selectKey>
        INSERT  INTO USER (username,birthday,sex,address)
        VALUES (#{username},#{birthday},#{sex},#{address})
    </insert>

### 聚合操作
	<select id="groupbyUsername" resultType="map">
        SELECT username,COUNT(*) as count FROM USER GROUP BY username ORDER BY count DESC
    </select>
* resultType 这里返回的数据类型是List<Map<String,Object>> 
	所以resultYpe对应的最小粒度是map。

### 分页
	<select id="fenye" parameterType="Map" resultType="com.springmvc.domain.User">
        SELECT * FROM USER limit #{start},#{count}
    </select>

    @RequestMapping("fenye")
    public String fenye(Model model, Integer start) {
        Map<String ,Object> parameter = new HashMap<>();
        parameter.put("start",start*5);
        parameter.put("count",5);
        List<User> datas = userService.fenye(parameter);
        System.out.println(datas);
        model.addAttribute("name", datas);
        return "demo";
    }

## 动态sql操作
### 动态查询
	    <!--动态sql 查询-->
    <select id="dynamicSqlwithSelect" parameterType="map" resultType="com.springmvc.domain.User">
        SELECT * FROM USER
        <where>
            <if test="pname!=null">
                AND username LIKE #{pname}
            </if>
            <if test="paddress!=null" >
                AND address=#{paddress}
            </if>

        </where>

    </select>

### 动态更新
	<update id="dynamicSqlUpdate" parameterType="com.springmvc.domain.User">
        update USER
          <set>
              <if test="username!=null">
                  username=#{username},
              </if>
              <if test="address != null">
                  address=#{address},
              </if>
          </set>
         WHERE id=#{id}

    </update>

* 不用写set  最后“,” 自动删除。

### 动态删除
	<delete id="dynamicSqldelete" >
        delete from user where id in
        <foreach item="ids" collection="list" open="(" close=")" separator=",">
            ${ids}
        </foreach>
    </delete>

### 动态插入
    <sql id="key">
        <trim suffixOverrides=",">
            <if test="id!=null">
                id,
            </if>
            <if test="username != null">
                username,
            </if>
            <if test="birthday != null">
                birthday,
            </if>
            <if test="address != null">
                address,
            </if>
            <if test="sex != null">
                sex,
            </if>
        </trim>

    </sql>

    <sql id="val">
        <trim suffixOverrides=",">
            <if test="id !=null">
                #{id},
            </if>
            <if test="username!=null">
                #{username},
            </if>
            <if test="birthday != null">
                #{birthday},
            </if>
            <if test="address != null">
                #{address},
            </if>
            <if test="sex!=null">
                #{sex},
            </if>
        </trim>
    </sql>

    <insert id="dynamicInsert" parameterType="com.springmvc.domain.User">
        insert into USER(<include refid="key"/>) VALUES (<include refid="val"/>)
    </insert>


##联表查询
##### bean
    public class User implements Serializable {

        private Integer id;
        private String username="";
        private String birthday;
        private String sex;
        private String address;
    
        private List<Address> addressList = new ArrayList<>();
    
        public Integer getId() {
            return id;
        }
    
        public void setId(Integer id) {
            this.id = id;
        }
    
        public String getUsername() {
            return username;
        }

        public void setUsername(String username) {
            this.username = username;
        }
    
        public String getBirthday() {
            return birthday;
        }
    
        public void setBirthday(String birthday) {
            this.birthday = birthday;
        }
    
        public String getSex() {
            return sex;
        }
    
        public void setSex(String sex) {
            this.sex = sex;
        }

        public String getAddress() {
            return address;
        }
    
        public void setAddress(String address) {
            this.address = address;
        }
    
        public List<Address> getAddressList() {
            return addressList;
        }
    
        public void setAddressList(List<Address> addressList) {
            this.addressList = addressList;
        }

        @Override
        public String toString() {
            return "User{" +
                    "id=" + id +
                    ", username='" + username + '\'' +
                    ", birthday='" + birthday + '\'' +
                    ", sex='" + sex + '\'' +
                    ", address='" + address + '\'' +
                    ", addressList=" + addressList +
                    '}';
        }
    }

------------------------------------------------------------------
    public class Address {
        private int id;
        private String name;
        private User user;
    
        public int getId() {
            return id;
        }
    
        public void setId(int id) {
            this.id = id;
        }
    
        public String getName() {
            return name;
        }
    
        public void setName(String name) {
            this.name = name;
        }
    
        public User getUser() {
            return user;
        }

        public void setUser(User user) {
            this.user = user;
        }
    
    
        @Override
        public String toString() {
            return "Address{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    '}';
        }
    }    

### 一对一 一个地址对应一个用户
#### AddressMapper.xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
    
    <mapper namespace="com.springmvc.dao.AddressMapper">
    
        <resultMap id="address" type="com.springmvc.domain.Address">
            <id property="id" column="id"/>
            <result property="name" column="name"></result>
        </resultMap>
    
        <resultMap id="address2" type="com.springmvc.domain.Address">
            <id property="id" column="id"/>
            <result property="name" column="name"></result>
            <association property="user"    resultMap="com.springmvc.dao.UserMapper.UserMap"/>
        </resultMap>
    

        <select id="findById" parameterType="int" resultMap="address2" >
            SELECT a.name,u.username FROM address a INNER JOIN user u ON a.uid=u.id  AND a.id=#{id}
        </select>


    </mapper>
    
* association 联合查询 相当于引入。
* property 属性 type 指向的bean的属性名称。
* resultMap 由namespace+resultMap的id组成。
* 这里的select resultMap选择是address2.

### 一对多  一个用户对应多个地址
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
    <mapper namespace="com.springmvc.dao.UserMapper">
    
    
        <resultMap id="UserMap" type="com.springmvc.domain.User">
            <id property="id" column="id"/>
            <result property="username" column="username"></result>
            <result property="birthday" column="birthday"></result>
            <result property="sex" column="sex" ></result>
            <result property="address" column="address"></result>
            <association property="addressList" resultMap="com.springmvc.dao.AddressMapper.address"/>
        </resultMap>
    
        <select id="findAddress" parameterType="int" resultMap="UserMap">
             SELECT u.username,a.* FROM user u INNER JOIN  address a ON a.uid=u.id AND u.id=#{id}
        </select>
    
    </mapper>




















