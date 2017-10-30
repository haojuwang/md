# Filter 过滤器

## Filter过滤器 是什么
	java web 中的过滤器可以拦截所有访问web资源的请求或响应操作。

## Filter过滤器 有什么用
* 拦截
* 数据处理
* 统一控制

## Filter过滤器 如何用
### 使用步骤
* 创建一个类实现Filter接口
* 重写接口中方法 doFilter 方法是真正过滤的。
* 在web.xml文件中配置
###### 注意
	在Filter的doFilter方法如果没有执行chain.doFilter(request,response)
	那么资源是不会被访问到的。

##### 示例	
    public class Myfilter2 implements Filter {

		public void destroy() {
		}
		
		public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
			System.out.println("Myfilter2 执行了");
			//放行
			chain.doFilter(request, response);
			System.out.println("Myfilter2 拦截结束了");
		}

		public void init(FilterConfig fConfig) throws ServletException {
		}

	}
------------------------------------------------------------------------------
	  <filter>
	      <filter-name>MyFilter3</filter-name>
	      <filter-class>cn.leyue.filter.MyFilter3</filter-class>
	  </filter>
	  <filter-mapping>
	      <filter-name>MyFilter3</filter-name>
	      <url-pattern>/servlet/indexServlet</url-pattern>
	  </filter-mapping>
-------------------------------------------------------------------------------	  

### FilterChain
	FilterChain是servlet 容器为开发人员提供的对象，他提供了对某一个资源的已过滤请求调用链的视图。
	过滤器使用FilterChain调用链中的下一个过滤器。
	如果调用的过滤器是链中的最后一个过滤器 则调用链末尾的资源。

	怎样可以形成一个Filter链？
		只要多个Filter 对同一个资源进行拦截可以形成Filter链
	怎样确定Filter的执行顺序
		由<filter-mapping>来确定
	
![filter过滤器.png](http://upload-images.jianshu.io/upload_images/1197747-aeaabd9c9ccfcb84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	

### Filter 生命周期
* 当服务器启动，会创建Filter对象，并调用init方法，只调用一次
* 当访问资源时，路径与Filter的拦截路径匹配，会执行Filter中的doFilter方法，这个方法
是真正拦截操作的方法。
* 当服务器关闭时，会调用Filter的destroy 方法来进行销毁操作。

### FilterConfig
	在Filter的init 方法上有一个参数，类型就是FilterConfig，
	FilterConfig它是Filter的配置对象，它可以完成以下功能：
	* 获取Filter名称
	* 获取Filter初始化参数
	* 获取ServletContext 对象
	getFilterName()  getInitParameter(String name) 
	getInitParameterNames() getServletContext();

	* 怎样在Filter中获取一个FilterConfig对象？
		private FilterConfig fConfig;
		public void init(FilterConfig fConfig) throws ServletException {
			this.fConfig = fConfig;
		}

### Filter 配置
###### 基本配置
	<filter>
		<filter-name>filter</filter-name>
		<filter-class>filter类的包名.类名</filter-class>

	</filter>	
	<filter-mapping>
		<filter-name>filter 名称</filter-name>	
		<filter-pattern>路径</filter-pattern>
	</filter-mapping>	

#### <url-pattern>
	完全匹配 以“/demo”开始，不包含通配符
	目录匹配 以“/”开始，以*结束
	扩展名匹配 *.xxx 不能写成 /*.xxx

#### <servlet-name>	
	<filter>
      <filter-name>MyFilter3</filter-name>
      <filter-class>cn.leyue.filter.MyFilter3</filter-class>
	</filter>
	   
	<filter-mapping>
	    <filter-name>MyFilter3</filter-name>
	    <url-pattern>/servlet/indexServlet</url-pattern>
	</filter-mapping>

#### <dispatcher>	
	<filter-mapping>
      <filter-name>MyFilter3</filter-name>
      <url-pattern>/servlet/*</url-pattern>
      <dispatcher>REQUEST</dispatcher>
      <dispatcher>FORWARD</dispatcher>
      
  	</filter-mapping>

可以取的值有  REQUEST  FORWARD  ERROR  INCLUDE	
它的作用是：当以什么方式去访问web资源时，进行拦截操作.	
1.REQUEST 当是从浏览器直接访问资源，或是重定向到某个资源时进行拦截方式配置的 它也是默认值
2.FORWARD 它描述的是请求转发的拦截方式配置
3.ERROR 如果目标资源是通过声明式异常处理机制调用时，那么该过滤器将被调用。除此之外，过滤器不会被调用。
4.INCLUDE 如果目标资源是通过RequestDispatcher的include()方法访问时，那么该过滤器将被调用。除此之外，该过滤器不会被调用

### md5 加密
	public static String md5(String plainText) {
		byte[] secretBytes = null;
		try {
			secretBytes = MessageDigest.getInstance("md5").digest(
					plainText.getBytes());
		} catch (NoSuchAlgorithmException e) {
			throw new RuntimeException("没有md5这个算法！");
		}
		String md5code = new BigInteger(1, secretBytes).toString(16);
		for (int i = 0; i < 32 - md5code.length(); i++) {
			md5code = "0" + md5code;
		}
		return md5code;
	}

### 全局的编码过滤器
	public class EncodingFilter implements Filter {

	/**
	 * Default constructor.
	 */
	public EncodingFilter() {
		// TODO Auto-generated constructor stub
	}

	/**
	 * @see Filter#destroy()
	 */
	public void destroy() {
		// TODO Auto-generated method stub
	}

	/**
	 * @see Filter#doFilter(ServletRequest, ServletResponse, FilterChain)
	 */
	public void doFilter(ServletRequest request, ServletResponse response,
			FilterChain chain) throws IOException, ServletException {
		HttpServletRequest req = (HttpServletRequest) request;
		req.setCharacterEncoding("utf-8");
		
		
		MyRequest myRequst = new MyRequest(req);

		chain.doFilter(myRequst, response);
	}

	/**
	 * @see Filter#init(FilterConfig)
	 */
	public void init(FilterConfig fConfig) throws ServletException {
		// TODO Auto-generated method stub
	}

	/**
	 * 实现与被包装对象相同的接口
	 *  定义一个与包装类相同对象的引用 
	 *  定义一个构造方法，把被包装对象传过来 
	 *  对于不需要改写方法，直接调用
	 * 对于无需要改写方法，写自己的方法体
	 */
	class MyRequest extends HttpServletRequestWrapper {
		HttpServletRequest request;

		public MyRequest(HttpServletRequest request) {
			super(request);
			this.request = request;
		}

		@Override
		public String getParameter(String name) {
			if(getParameterMap().get(name)==null) {
				return null;
			}
			return getParameterMap().get(name)[0];
		}

		@Override
		public String[] getParameterValues(String name) {
			return getParameterMap().get(name);
		}

		private boolean flag = true;

		@Override
		public Map<String, String[]> getParameterMap() {
			//获取请求方式
			String method = request.getMethod();
			if(method.equalsIgnoreCase("post")) {
				//处理乱码
				try {
					request.setCharacterEncoding("utf-8");
					return request.getParameterMap();
				} catch (UnsupportedEncodingException e) {
					e.printStackTrace();
				}
				
			} else if(method.equalsIgnoreCase("get")) {
				Map<String, String[]> map = this.request.getParameterMap();
				if (!flag) {
					for (Map.Entry<String, String[]> m : map.entrySet()) {
						String[] values = m.getValue();
						for (int i = 0; i < values.length; i++) {
							try {
								//get方式处理乱码
								values[i] = new String(values[i].getBytes("ISO-8859-1"), "utf-8");
							} catch (UnsupportedEncodingException e) {
								e.printStackTrace();
							}
						}
					}
					flag =true;
				}
				return map;
				
			}
			return super.getParameterMap();
			
		}
	}

}

-------------------------------------------------------
2016.11.6


























