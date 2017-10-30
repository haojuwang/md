## java 使用memcached
#### 导入jar
	java_memcached-release_2.6.6.jar
	commons-pool-1.5.6.jar
	slf4j-api-1.6.1.jar
	slf4j-simple-1.6.1.jar

#### 示例代码
##### 创建一个memcached 工厂类
```
	public class CacheHelper {

   	 	private static MemCachedClient mcc = new MemCachedClient();
		
   	 	private CacheHelper() {
   	 	}

    	static {
    	    String[] servers = {"71.0.0.29:2222", "71.0.0.29:2223"};
    	    Integer[] weights = {1, 2};
	
	
    	    SockIOPool pool = SockIOPool.getInstance();
	
    	    pool.setServers(servers);
    	    pool.setWeights(weights);
    	    pool.setInitConn(5);
    	    pool.setMinConn(5);
    	    pool.setMaxConn(250);
    	    pool.setMaxIdle(1000 * 60 * 60 * 6);
    	    pool.setMaintSleep(30);
    	    pool.setNagle(false); // 禁用nagle算法
    	    pool.setSocketConnectTO(0);
    	    pool.setSocketTO(3000); //3秒超时
    	    pool.setHashingAlg(3);
	
    	    pool.initialize();
    	}

    	public static MemCachedClient getMemCachedClient() {
    	    return  mcc;
    	}
	}
```
--------------
测试
```
 MemCachedClient mcc = CacheHelper.getMemCachedClient();
 mcc.add("k8", "1234455");

 MemCachedClient mcc = CacheHelper.getMemCachedClient();
       
        UserModel user = new UserModel("12322222", "lihao", 28);
        boolean is = mcc.add("user2",user);





```

```
public void setInitConn( int initConn ) 
设置开始时每个cache服务器的可用连接数

public void setMinConn( int minConn ) 
设置每个服务器最少可用连接数

public void setMaxConn( int maxConn ) 
设置每个服务器最大可用连接数

 public void setMaxIdle( long maxIdle )
  设置可用连接池的最长等待时间

public void setMaintSleep( long maintSleep )
设置连接池维护线程的睡眠时间 ,设置为0,维护线程不启动。维护线程主要通过log输出
socket的运行状况,监测连接数目及空闲等待时间等参数以控制连接创建和关闭。

 public void setNagle( boolean nagle )
设置是否使用Nagle算法,因为我们的通讯数据量通常都比较大(相对TCP控制数据)而且要求响 应及时,因此该值需要设置为false(默认是true)

ublic void setSocketTO( int socketTO ) 
设置socket的读取等待超时值

public void setSocketConnectTO( int socketConnectTO )
 设置socket的连接等待超时值

 public void setHashingAlg( int alg ) 
 设置hash算法
	alg=0 使用String.hashCode()获得hash code,该方法依赖JDK,可能和其他客户端不兼容,建议不使用 	
	alg=1 使用original 兼容hash算法,兼容其他客户端
	alg=2 使用CRC32兼容hash算法,兼容其他客户端,性能优于original算法
	alg=3 使用MD5 hash算法
	采用前三种hash算法的时候,查找cache服务器使用余数方法。采用最后一种hash算法查找cache 	服务时使用一致性hash方法

public void initialize() 设置完pool参数后最后调用该方法,启动pool。

```

#### memcached 和Spring 继承
```
	<!--memcached 客户端 SocketPool-->
	<bean id="memcachedPool" class="com.danga.MemCached.SockIOPool" 
	factory- method="getInstance"
	 init-method="initialize" 
	 destroy-method="shutDown">
		<constructor-arg><value>neeaMemcachedPool</value></constructor-arg> <property 	name=	"servers">
		<list> <value>192.168.1.106:2222</value> <value>192.168.1.106:2223</value>
		         </list>
		     </property>
		<property name="weights"> <list>
		               <value>1</value>
		               <value>2</value>
		           </list>
		</property>
		<property name="initConn">
	         <value>5</value>
	     </property>
		<property name="minConn">
			 <value>5</value>
		</property>
		<property name="maxConn"> 
			<value>250</value>
		</property>
		<property name="maintSleep">
    	    <value>30</value>
    	</property>
    	<property name="nagle">
    	    <value>false</value>
		</property>
		<property name="maxIdle">
    	    <value>6000</value>
    	</property>
		<property name="socketTO">
			 <value>3000</value>
    	</property>
	</bean>

	<!--memcached client-->
	<bean id="memCachedClient" class="com.danga.MemCached.MemCachedClient">
	<constructor-arg> 
		<value>neeaMemcachedPool</value>
    </constructor-arg>
 </bean>

```




























