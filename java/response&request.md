# response&request

## response
#### 乱码
	服务器默认的编码为IOS-8859-1，不支持中文

	告诉服务器使用utf-8解析文本
	response.setCharacterEncoding("utf-8")

	告诉浏览器使用utf-8编码
	response.setContentType("text/html;charset=utf-8");

#### 文件下载
	//字节流编码
	ServletOutputStream out = response.getOutputStream();	

	//找到 图片文件
	String path = this.getServletContext().getRealPath("/WEB-INF/图片.png");
	FileInputStream file = new FileInputStream(path);
	
	//设置下载的文件名
	String filename = path.substring(path.lastIndexOf("/")+1);
	filename = URLEncoder.encode(filename, "utf-8");
	System.out.println(filename);
	//response 设置下载 
	response.setHeader("content-disposition","attachment;filename="+filename);
	response.setHeader("content-type","image/jpeg");
	
	//图片以流的方式写入到response
	byte[] b = new byte[1024];
	int  len = -1;
	ServletOutputStream out = response.getOutputStream();
	while((len = file.read(b))!=-1) {
		out.write(b, 0, len);
	}
	out.close();
	file.close();

#### 生成验证码
		int width = 110;
		int height = 25;
		//创建画布
		BufferedImage img = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
		//创建画笔
		Graphics paint = img.getGraphics();
		//画背景
		paint.setColor(Color.PINK);
		paint.fillRect(1, 1, width-2, height-2);
		//画边框
		paint.setColor(Color.RED);
		paint.drawRect(0, 0, width-1, height-1);
		//画内容
		Random rand = new Random();
		paint.setFont(new Font("宋体",Font.BOLD|Font.ITALIC,15));
		paint.setColor(Color.BLUE);
		for(int i=0;i<4;i++) {
			paint.drawString(rand.nextInt(10)+"", (i+1)*20, 18);
		}
		//画线条
		paint.setColor(Color.decode("#0074D9"));
		for(int i=0;i<9;i++) {
			paint.drawLine(rand.nextInt(width), rand.nextInt(height), rand.nextInt(width), rand.nextInt(height));	
		}
		
		//输出到页面
		ImageIO.write(img, "jpg", resp.getOutputStream());

#### 第三方工具 ValidateCode 生成验证码
	ValidateCode vc = new ValidateCode(110, 25, 4, 9);
	String code  = vc.getCode();
	vc.write(resp.getOutputStream());


#### 刷新页面 refresh
	//每秒刷新一次
	response.setIntHeader("refresh", 1);	

	// 3秒跳转页面
	response.setHeader("refresh", "3;url=/firstweb/test.html");

	//重定向
	response.sendRedirect("/firstweb/test.html");

#### 设置响应码
	setStatus(int sc)
#### 响应头
	sendRedirect(String location) 请求重定向
	setHeader(String name,String value) 设置响应头信息

#### 设置不缓存	
	response.setHeader("pragma", "no-cache");
	response.setHeader("cache-control", "no-cache");
	response.setIntHeader("expires", 0);	

#### 相应正文（主题）
* getWrite() 字符串输出
* getOutputStream() 字节输出流
* setCharacterEncoding(String charset) 告知服务器使用什么编码
* setContentType("text/html;charset=utf-8")	

------------------------------------------------------------------------
## request

#### 请求行
	get: http://localhost:8080/firstweb/request1?lihao=李浩

	getMethod() 获得请求方式
	getRequestURL() 获取整个url
	getRequestURI() 返回请求行种的资源名部分  /firstweb/request1
	getContentPath() 当前应用的虚拟目录 /firstweb
	getQueryString() 返回请求行中的参数部分 lihao=李浩
	URLDecoder.decode(req.getQueryString())

#### 请求消息头
	
	String      getHeader(String name) 根据头名称得到头信息值
	Enumeration getHeaderNames() 得到所有头信息name
	Enumeration getHeaders(String name) 根据头名称得到相同名称头信息值


#### 请求正文
获取表单数据相关的方法
* getParameter(name) 根据表单中name属性的命，获取value 属性的值方法
* getParameterValues(name )	 专为为复选框取值提供的方法
* getParameterNames() 得到表单提交的所有name的方法
* getParameterMap 得到表单提交的所有值的方法（做框架用，非常实用）
* getInputStream 以字节流的方式得到所有表单数据

###### 	getParameter
	String userName = request.getParameter("userName");		

###### 	getParameterValues
	String[] hobbys = request.getParameterValues("hobby");

###### getParameterMap
	User user = new User();
		System.out.println("封装数据前：" + user);
		Map<String, String[]> params = request.getParameterMap();
		for (Map.Entry<String, String[]> entity : params.entrySet()) {
			String name = entity.getKey();
			String[] values = entity.getValue();
			PropertyDescriptor pd;
			try {
				pd = new PropertyDescriptor(name, User.class);
				Method setter = pd.getWriteMethod();
				if (values.length == 1) {
					setter.invoke(user, values[0]);
				} else {
					setter.invoke(user, (Object) values);
				}
				System.out.println("封装数据前：" + user);

			} catch (Exception e) {
				e.printStackTrace();
			}
		}

		System.out.println("封装数据后：" + user);

	BeanUtils.populate(user, request.getParameterMap());


###### getParameterNames
	Enumeration<String> names = request.getParameterNames();
	while (names.hasMoreElements()) {
		String name = names.nextElement();
		String[] values = request.getParameterValues(name);
		for (int i = 0; values != null && i < values.length; i++) {
			System.out.println(name + " = " + values[i]);
		}
	}

###### getInputStream
	ServletInputStream is = request.getInputStream();
	 byte[] buffer = new byte[1024];
	 int len = -1;
	 while((len = is.read(buffer)) != -1) {
		 System.out.println(URLDecoder.decode(new String(buffer,0,len,"utf-8")));
	 }

#### 与操作非表单相关的方法（request也是一个域对象）
* setAttribute(String name,Object value)
* getArrtibute(String name)
* removeAttribute(String name)


#### 与请求转发相关的方法
	getRequestDispatcher(String path).forward(request,response)
	.include(request,response)

#### 请求编码
	post解决
	request.setCharacterEncoding("utf-8)

	get 方式编码
	String name = new String(name.getBytes("iso-8859-1"),"utf-8")	












