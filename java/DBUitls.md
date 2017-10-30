# DBUitls
## commons-dbutils 是什么
	commons-dbutils 是Apache组织提供的一个JDBC工具类库，
	它是JDBC的简单封装，学习成功极低，并且使用dbutils能极大简化jdbc编码的工作量，
	同时也不会影响程序的性能。
## 	commons-dbutils 有什么用
* 简化jdbc编码
* 优化数据库结果集操作
* 数据库连接池优化 数据库连接  dbutils优化数据库结果集


## DBUtils 的三个核心对象
* QueryRunner类
* ResultSetHandler接口
* DBUtils类

##### QueryRunner类
	QueryRunner中提供对sql语句操作的API
	它主要有三个方法：
		* query()用于执行select
			该方法会自行处理 PreparedStatement 和 ResultSet 的创建和关闭。

		* update()用于执行 insert update delete
		* batch() 批处理

##### ResultSetHandler接口		
	定义select操作后，怎样封装结果集。
	以下 实现ResultSetHandler 接口类
* ArrayHandler： 把结果集中的第一行数据转成对象数组。
* ArrayListHandler： 把结果集中的每一行数据都转成一个数组，再存放在list中
* BeanHandler: 把结果集中的第一行数据封装到一个对应的JavaBean实例中
* BeanListHandler: 将结果集中的每一行数据都封装到一个对应的JavaBean实例中，存放到List里 
* ColumnListHandler: 将结果集中某一列的数据存放到List中。
* KeyedHandler(name)：将结果集中的每一行数据都封装到一个Map<列名,列值>里，再把这些map再存到一个map里，其key为指定的key。
* MapHandler: 将结果集中的第一样数据封装到一个Map里，key是列明，value就是对应的值
* MapListHandler：将结果集中的每一行数据都封装到一个Map里，然后再存放到List



##### DBUtils类	
	它就是一个工具类，定义了关闭资源与事务处理的方法
* close()方法检查所提供的参数是不是NULL，如果不是的话，它们就关闭Connection、Statement和ResultSet。

* closeQuietly()这一类方法不仅能在Connection、Statement和ResultSet为NULL情况下避免关闭，还能隐藏一些在程序中抛出的SQLEeception

* commitAndCloseQuietly() 用来提交连接，然后关闭连接，并且在关闭连接时不抛出SQL异常。

* loadDriver() 这一方装载并注册JDBC驱动程序，如果成功就返回true。使用该方法，你不需要捕捉这个异常ClassNotFoundException。

##### 代码示例
	数据库连接池
	public class C3P0Util {
		//得到一个数据源
		private static DataSource dataSource = new ComboPooledDataSource();
		public static Connection getConnection(){
			try {
				return dataSource.getConnection();
			} catch (SQLException e) {
				e.printStackTrace();
				throw new RuntimeException("服务器错误");
			}
		}
		
		public static DataSource getDataSource() {
			return dataSource;
		}

		public static void setDataSource(DataSource dataSource) {
			C3P0Util.dataSource = dataSource;
		}

		public static void release(Connection conn, Statement stmt, ResultSet rs) {

			if (rs != null) {
				try {
					rs.close();
				} catch (Exception e) {
					e.printStackTrace();
				}
				rs = null;
			}
			if (stmt != null) {
				try {
					stmt.close();
				} catch (Exception e) {
					e.printStackTrace();
				}
				stmt = null;
			}
			if (conn != null) {
				try {
					conn.close();
				} catch (Exception e) {
					e.printStackTrace();
				}
				conn = null;
			}
		}

	}
--------------------------------------------------------------------------
	//insert
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	qr.update("insert into tags(tag,name,money,author) value(?,?,?,?)", "乱码","否", 100.2045, "李浩");
--------------------------------------------------------------------------
	//update 
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	qr.update("update tags set tag=? where id=?","90后",2);
--------------------------------------------------------------------------
	//delete
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	qr.update("delete from tags where id=3");
--------------------------------------------------------------------------
	//batch
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	Object[][] params = new Object[10][];
	for(int i=0;i<params.length;i++) {
		params[i]= new Object[]{"这是tag"+i,"李浩"+i,10+i,"小米"+i};
	}
	qr.batch("insert into tags(tag,name,money,author) value(?,?,?,?) ", params);
--------------------------------------------------------------------------
	//select 结果集
	ArrayHandler:适合取1条记录。把该条记录的每列值封装到一个数组中Object[]
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	Object[] arr = qr.query("select * from tags where id=5",new ArrayHandler());
	for(int i=0;i<arr.length;i++) {
		System.out.println(arr[i]);
	}
--------------------------------------------------------------------------
	ArrayListHandler:适合取多条记录。把每条记录的每列值封装到一个数组中Object[]，把数组封装到一个List中
	 QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	  List<Object[]> datas = qr.query("select * from tags", new ArrayListHandler());
	  for(Object[] data:datas) {
			for(int i=0;i<data.length;i++) {
				System.out.print(data[i]+"\t");
			}
			System.out.println("-------------------------------------");
	  }

--------------------------------------------------------------------------
	ColumnListHandler:取某一列的数据。封装到List中。  
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	List<Object> datas = qr.query("select * from tags", new ColumnListHandler(1));
	for(Object data:datas) {
		System.out.println(data);
	}

--------------------------------------------------------------------------
	MapHandler:适合取1条记录。把当前记录的列名和列值放到一个Map中
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	Map<String, Object> datas = qr.query("select * from tags", new MapHandler());
	for(Map.Entry<String, Object> data:datas.entrySet()) { 
		System.out.println(data.getKey()+"  "+data.getValue());
	}

--------------------------------------------------------------------------
	MapListHandler:适合取多条记录。把每条记录封装到一个Map中，再把Map封装到List中
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	List<Map<String, Object>> datas = qr.query("select * from tags",new MapListHandler());
	for(Map<String, Object> data2:datas) {
		for(Map.Entry<String, Object> data:data2.entrySet()) { 
			System.out.println(data.getKey()+"  "+data.getValue());
		}
		System.out.println("--------------------------------------");
	}

--------------------------------------------------------------------------
	ScalarHandler:适合取单行单列数据
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	Object data = qr.query("select count(*) from tags",new ScalarHandler());
	System.out.println(data);

--------------------------------------------------------------------------
	BeanHandler：讲结果集中的第一行封装到一个对应的javabean实例中
	QueryRunner qr =new QueryRunner(C3P0Util.getDataSource());
	Tag tag = qr.query("select * from tags where id=6", new BeanHandler<Tag>(Tag.class));
	System.out.println(tag);

--------------------------------------------------------------------------
	BeanListHandler: 将结果集中的每一行数据都封装到一个对应的JavaBean实例中，存放到List里 
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	List<Tag> datas = qr.query("select * from tags ",new BeanListHandler<Tag>(Tag.class));
	for(Tag tag : datas) {
		System.out.println(tag);
	}

--------------------------------------------------------------------------
	KeyedHandler(name)：将结果集中的每一行数据都封装到一个Map<列名,列值>里，再把这些map再存到一个map里，其key为指定的key
	QueryRunner qr = new QueryRunner(C3P0Util.getDataSource());
	Map<Object, Map<String, Object>> datas = qr.query("select * from tags ",new KeyedHandler(2));
	for(Map.Entry<Object, Map<String,Object>> data2 : datas.entrySet()) {
		System.out.println("key "+data2.getKey());
		for(Map.Entry<String,Object> data:data2.getValue().entrySet()) {
			System.out.println(data.getKey() +"  "+ data.getValue());
		}
		System.out.println("------------------------------------");
	}
--------------------------------------------------------------------------
2016.10.30




