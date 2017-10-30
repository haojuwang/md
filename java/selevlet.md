# servlet

## servlet 声明周期
 	实例化 -> 初始化 -> 服务 -> 销毁

 	出生： （实例化 -> 初始化） 第一次访问Servlet就出生
 	活着：（服务） 应用活着，Servlet 就活着
 	死亡： （销毁）应用卸载了Servlet 就销毁

 	实例  init service destroy

 	如何让Servlet 在服务器启动时就创建
 	在web.xml servlet 中设置
 	<load-on-startup>2</load-on-startup>


## Servlet 的线程安全
	单例：每次访问多线程
	解决县城安全问题的最佳方法：不要写全局变量，而写局部变量 	



## Servlet 获取配置信息
### ServletConfig 使用
####可以获取servlet配置信息
	this.getServletConfig().getInitParameter(key)	

####可以获得Servlet对象

## ServletContext
SerletContext 代表的是整个应用，一个应用只有一个ServletContext对象。单例

#### 作用
##### 1,域对象：在一定范围内（当前应用），使多个Servlet共享数据
	常用方法：
		this.getServletContext.setAtrribute(String name,Object value)	
		this.getServletContext.getAttribute(String name)
		this.getServletContext.moveAttribute(String name)

##### 2，获取全局配置信息
	web.xml
	<contxt-param>
		<param-name>encoding</contxt-param>
		<param-value>utf-8</param-value>
	</contxt-param>>

	this.getServletContext.getInitParameter("encoding")


##### 3,获取资源路径
	this.getServletContext.getRealPath(String path)

	private void getPropertie(HttpServletResponse resp) {
		ServletContext sc = this.getServletContext();
		String path = sc.getRealPath("WEB-INF/a.properties");
		Properties ps = new Properties();
		try {
			ps.load(new FileInputStream(path));
			PrintWriter out = resp.getWriter();
			out.println(ps.get("name"));
			out.println(ps.get("age"));
			out.println(ps.get("sex"));
			out.println(sc.getClass().getName());
		} catch (Exception e) {
			e.printStackTrace();
		}
	}































