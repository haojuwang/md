# cookie
Cookie 数据是由客户端来保存和携带的，所以称之为客户端技术

## 属性
	Cookie cookie = new Cookie("lastAccessTime", System.currentTimeMillis()+"");

* name 名称不能唯一确定一个Cookie。路径可能不同。
* value 不能存中文。
* path  默认值是写Cookie的那个程序的访问路径	
	http://localhost:8082/firstweb/cookie1
	* path 就是firstweb/cookie1
	* 客户端在访问服务器另外资源时，根据访问的路径来决定是否带有着Cookie到服务器
	* 当前访问的路径如果是以cookie的path开头的路径，浏览器就带，否则不带

* maxAge: cookie的缓存时间，默认是-1（默认存在浏览器的内存中），单位是秒
	* 负数：cookie 的数据存在浏览器缓存中
	* 0： 删除。路径要保持一致，否则可能删错人。
	* 正数： 缓存（持久化到磁盘上）的时间

#### 添加cookie
	//获取到cookie
		Cookie[] cookies = request.getCookies();
		if(cookies == null) {
			out.write("您还没有访问过");
		}
		for(int i=0;cookies !=null && i<cookies.length;i++) {
			if("lastAccessTime".equals(cookies[i].getName())) {
				Long l = Long.parseLong(cookies[i].getValue());
				System.out.println("过期时间："+cookies[i].getMaxAge());
				out.write("您最后访问的时间为："+new Date(l).toLocaleString()+"<a href='/firstweb//clear/cookie1' target= '_blank'>clear</a>");
				
			} 
		}
		
		Cookie cookie = new Cookie("lastAccessTime", System.currentTimeMillis()+"");
		//设置缓存时间5分钟
		cookie.setMaxAge(60*5);
		//设置缓存目录
		cookie.setPath(request.getContextPath());
		//返回cookie
		response.addCookie(cookie);

#### 删除cookie		
		Cookie[] cookies = request.getCookies();
		for(int i=0;cookies !=null && i<cookies.length;i++) {
			if("lastAccessTime".equals(cookies[i].getName())) {
				Cookie cookie = cookies[i];
				cookie.setValue("");
				//删除
				cookie.setMaxAge(0);
				cookie.setPath(request.getContextPath());
				response.addCookie(cookie);
				
				out.write("lastAccessTime  已经清空了" );
			}
		}

-------------------------------------------------------------------
# HttpSession
#### HttpSession 用途
* 它也是一个域对象：session servletContext request
* 同一个会话下，可以使一个应用的多个资源共享数据
* cookie 客户端技术，只能存字符串。HttpSession服务器端的技术，它可以存对象

#### 常用方法
把数据保存在HttpSession对象中，该对象也是一个域对象

##### setAttribute(String name,Object value)
##### getAttribute(String name)
##### remvoeArrtibute(String name)
##### getId()
##### setMaxInactiveInterval(int interval) 设置session的存活时间 单位秒
##### invalidate() 销毁session
		response.setContentType("text/html;charset=utf-8");
		String name = request.getParameter(" name");
		String age  = request.getParameter("age");
		HttpSession session = request.getSession();

		//name =  new String(name.getBytes("ISO-8859-1"), "UTF-8"); 
		System.out.println(name);
		session.setAttribute("name", name);
		session.setAttribute("age", age);
		//10秒失效
		session.setMaxInactiveInterval(10);
		PrintWriter out = response.getWriter();
		out.write("sessionId = "+session.getId()+"  name= "+name );




#### getSession() 内部执行原理
* 1,获取名称为JSESSIONID的cookie的值
* 2，没有这样的cookie，创建一个新的HttpSession对象，分配一个唯一的SessionId,并且向客户端写了一个名为JSESSIONID=sessionId的cookie
* 3,有这样的Cookie，获取cookie的值（即HttpSession对象的值），从服务器的内存中根据Id找那个HttpSession对象
	找到了：取出继续为你服务
	找不到：从2开始




