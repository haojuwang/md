#jsp

## jsp概述
* jsp全称是 Java Server Page，它和servlet技术一样，都是SUN公司定义的一种用于开发动态web资源的技术。jsp实际上就是servlet
* jsp = html+java
* jsp 主要用于显示数据


## jsp 的原理
翻译-> 编译 -> 执行
demo.jsp 翻译成 _demo_jsp.java
 _demo_jsp.java 编译   _demo_jsp.class

## jsp作用
* Servlet:控制器。重点编写java代码逻辑（**获取表单数据，处理业务逻辑，分发转向**）。
* jsp: 代码显示模板，**重点在于显示数据**。 

## jsp的基本语法

### jsp模板元素
* jsp 页面中的HTML内容称之为jsp模板元素。
* jsp 模板元素定义了网页的骨架，即定义了页面的结构和外观。

## jsp脚本

#### 1，jsp 脚本片段
###### 语法
	<%
		//多行java代码
	%>
	<% 
		out.write("hello"); 
		out.write("142");
	%>


###### 注意
* 	jsp 脚本片段只能初心java代码，不能出现其他模板元素，jsp引进在翻译jsp页面中，会将jsp脚步片段中的jva代码原封不动的放倒servlet中的_jspService方法中
* jsp 脚本片段的java代码必须严格遵循java语法 每行语句结束要有（;）.
* 多个脚本之间的变量可以相互访问

#### 2 jsp表达式
jsp用于将程序的数据输出到客户端
###### 语法
	<%=变量表达式%>
	<%=2+8 %>
###### 注意	
	jsp表达式中的变量和表达式后面**不能有(;)**

#### 3，定义全局成员和静态块
###### 语法
	<%!  %>
	<%!
		int i=0;
	%>
	<%
		int j=0;
	%>
	<%
		out.write(" i= "+(i++)+"   j= "+(j++));
	%>
###### 注意
	i 是全局变量每次都会加1
	j 每次请求都会初始化

#### jsp 注释
* jsp注释<%-- 被注释的内容 --%> 特点：安全，省流量
* 网页注释：<!-- 网页注释 -->    特点：不安全，费流量
* 在查看源码时网页注释会显示出来 jsp注释不会显示


## jsp 三个指令
jsp指令是为jsp引擎二设计的，他们并不直接产生任何输出，而只是告诉浏览器如何处理jsp页面的其余部分。在jsp2.0规范中定义了三个指令
* page 指令
* include指令
* taglib 指令

###### 语法
	<%@ 指令 属性名1=“值1” 属性名2=“值2”  属性名3=“值3”>
	或者
	<%@ 指令 属性名1=“值1”%>
	<%@ 指令 属性名2=“值2”%>
	<%@ 指令 属性名3=“值3”%>

	<%@ page contentType="text/html; charset=UTF-8"  pageEncoding="UTF-8"%>

### 1，page
###### 作用
	用于定义jsp页面的各种属性，page指令最好是放在整个jsp页面的起始位置。
###### 属性
* 属性含义

|属性				|            定义 |
|-----------------------|:--------------------------:|
|language="java"        |指定JSPContainer用什么语言来编译，目前只支持Java语言。默认为JAVA|
|extends=”className”    |	定义此JSP网页产生的Servlet是继承哪个|
|import=”importList”    |     定义此JSP网页要使用哪些Java API|
|session = "true false"   | 决定此页面是否使用session对象，默认为ture|
|buffer=”none size in kb” | 决定输出流(Input stream)是否又缓冲区。默认为8kb|
|isThreadSafe=”true false”| 是否支持线程。默认为true|
|errorPage=”url”          | 如果此页发生异常，网页会重新指向一个url|
|isErrorPage=”true false” | 表示此页面是否为错误处理页面。默认为false|
|contentType=”text/html;charset=gb2312” | 表示MIME类型和JSP的编码方式|
|pageEncoding=”ISO-8859-1” | 	编码方式|
|isELLgnored=”true false”  | 表示是否在此JSP页面中EL表达式。true则忽略，反之false则支持。默认为false|

###### 例
	<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"  errorPage="error.jsp" %>
    <html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
	<title>Insert title here</title>
	</head>
	<body>
	<%
		int i= 10/0;
	%>
	</body>
	</html>
--------------------------------------------------------------------------
	error.jsp
	<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isErrorPage="true"%>
	
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
	<title>错误页面</title>
	</head>
	<body>
	errorMesg:
	<% out.write(exception.getMessage());%>

	</body>
	</html>
--------------------------------------------------------------------------
	web.xml配置error 页面
	 <error-page>
      <error-code>500</error-code>
      <location>/error.jsp</location>
  	</error-page>





### 2，include
* 静态包含：把其他资源包含到当前页面
	<% include file="/header.jsp"%>
	<% include file="/footer.jsp"%>

* 动态包含
<jsp:include page="/foorer"></jsp:include>

* 两者的区别：翻译的时间段不同
	* 静态在翻译时就把两个文件合并
	* 动态不会合并文件，当代码执行到include时，才包含另外一个文件内容
	* 能用静态就不用动态

* 例
--------------------------------------------------------------------------
	header.jsp 

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
	<title>Insert title here</title>
	</head>
	<body>
	我是是头部

--------------------------------------------------------------------------
	index.jsp
	<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
	<%@ include file="/header.jsp" %>
	我是内容
	<%@ include file="/footer.jsp" %>
--------------------------------------------------------------------------
	footer.jsp
	<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
	我是底部

	</body>
	</html>
--------------------------------------------------------------------------
### 3，taglib
* 作用：在jsp页面中导入jstl标签库。替换jsp中的java代码片段
* `<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>`

## jsp 六个动作
*	1,<jsp:include > 动态包含
*	2,<jsp:forward> 请求转发
*	3,<jsp:param> 设置请求参数
*	4,<jsp:useBean> 创建一个对象
*	5,<jsp:setProperty> 给指定的对象属性赋值
*	6,<jsp:getProperty> 取出指定对象的属性值
###### 例
	<jsp:useBean id="stu1" class="com.itheima.entity.Student"></jsp:useBean>
		 <jsp:setProperty property="name" name="stu1" value="jerry"/>
		 <jsp:getProperty property="name" name="stu1"/>
		 
	 <!-- http://localhost:8080/test.jsp?name=123 -->
	 <jsp:forward page="/7.jsp">
	 	<jsp:param value="123" name="name"/>
	 	<jsp:param value="333" name="pwd"/>
	 </jsp:forward>`



## jsp 9个内置对象
* 指在jsp的<%= %> 和<% %> 中可以直接使用的对象

|对象名				|            类型 |        说明      |
|-----------------------|:--------------------------:|--------------------------:|
|request       |javax.servlet.http.HttpServletRequest| |
|response     |javax.servlet.http.HttpServletResponse| |
|session		|javax.servlet.http.HttpSession| 由session="true" 开关|
|application   |javax.servlet.ServeletContext| |
|exception     | java.lang.Throwable|由 isErrorPage="false"开关|
|out           |  javax.servlet.jsp.JspWriter| 字符串输出流，相当于PrintWriter对象|
|page           | java.lang.Object 当前对象this | 当前servlet实例|
|pageContext  |javax.servlet.jsp.PageContext| |
|config       | javax.servlet.ServletConfig| |

#### pageContext 
##### 1， 本身也是一个域对象，他可以操作其他三个域对象（request session application）的数据
* void setAttribute(String name,Object o);
* Object getAttribute(String name);
* void removeAttribute(String name);
* 操作其它域对象的方法
* void setAttribute(String name,Object o，int Scope);
* Object getAttribute(String name,int Scope);
* void removeAttribute(String name,int Scope);
* scpoe的值：
* PageContext.PAGE_SCOPE 
* PageContext.REQUEST_SCOPE 
* PageContext.SESSION_SCOPE 
* PageContext.APPLICATION_SCOPE
* findAttribute(String name)；自动从page request session  application一次查找，找到了就取值，介乎查找

####### 例
	pageContext.setAttribute("test", "page",PageContext.PAGE_SCOPE);
	request.setAttribute("test", "request");
	session.setAttribute("test","session");
	application.setAttribute("test", "application");

##### 2，它可以创建其他的8个隐士对象
	<%
		pageContext.getSession();
		pageContext.getRequest()
		pageContext.getOut()
		//...
	%>
##### 3，提供了简易方法
* pageContext.forward("2.jsp");
* pageContext.include("2.jsp"); 


## 四大域对象，开发中使用场景
* PageContext:pageContext 数据存放在当前页面有效。开发时使用较小
* ServletRequest: request 数据存放在一次请求（转发）内有效，使用非常多。
* HttpSession: session 存放的数据在一次会话中有效，使用比较多。
* ServletContext: application 存放的数据在整个应用范围内都有效。因为范围太大，尽量少用


-------------------------------------------------------------------

## EL的具体功能
* 获取数据
* 运算
* 隐式对象：11个

#### 获取数据
###### EL表达式只能获取存在4个作用域的数据
	${u} 原理：pageContext.findAttribute("u");
	url>name=lihao 
	${param.name}
###### 获取对于 null这样的数据，在页面中表现为空字符串
###### 点（.） 运算符相当于调用了getter方法，点后面跟的是属性名
	<% 
		Book book = new Book("1", "android 开发大全", 29, "王子阳");
		request.setAttribute("book",book);
	%>

	${book.name }

###### []运算符：点能做的，它也能做；他不能做的，点不一定能做
	${book.name } === ${book['name'] } === ${book["name"] }


#### 运算
##### empty
		判断null,空字符串和没有元素的集合（即使集合对象本身不为null）都返回true
		<%
			String str = null;
			pageContext.setAttribute("str", str);
			
			String str1= "";
			pageContext.setAttribute("str1",str1);
			
			String str2="abc";
			pageContext.setAttribute("str2",str2);
			
			List<String> list1 = new ArrayList<String>();
			pageContext.setAttribute("list1", list1);
			
			List<String> list2 = new ArrayList<String>();
			list2.add("aa");
			pageContext.setAttribute("list2",list2);

		%>
		${ empty str } -----true <br/>
		${ empty str1 } -----true <br/>
		${ empty str2 } -----false <br/>
		${ empty list1 } -----true <br/>
		${ empty list2 } -----false <br/>
		${ empty str ? "str为空":"有值" }

		赋值 ${test = index+4 }
		取值 ${test}



#### 隐式对象
|EL	隐式对象引用名称|   类型 |   jsp内置对象名称   | 说明|
|-----------------|:--------------------------:|--------------:|---------------:|
|pageContext      | javax.servlet.jsp.PageCOntext| pageContent | 一样的	|
|pageSope         | java.util.Map<String,Object> | 没有对应的    | pageContext返回中存放的数据，页面范围	|
|requestScope     | java.util.Map<String,Object> | 没有对应的    |  请求范围数据	|
|sessionScope     | java.util.Map<String,Object> | 没有对应的    |  会话范围数据	|
|applicationScope | java.util.Map<String,Object> | 没有对应的    |  应用范围数据	|
|param            | java.util.Map<String,Object> | 没有对应的    |  一个请求参数	|
|paramValues      | java.util.Map<String,Object> | 没有对应的    |  重名请求参数	|
|header           | java.util.Map<String,Object> | 没有对应的    |  一个请求消息头	|
|initParam        | java.util.Map<String,Object> | 没有对应的    |  web.xml中全局参数	|
|cookie           | java.util.Map<String,Object> | 没有对应的    |  key:cookie对象的name值	|


------------------------------------------------------------------------------
## jstl 
* jstl 是什么
* jstl 解决什么问题
* jstl 如何使用

#### jstl 是什么
	jstl(JSP Standard Tag Library，JSP标准**标签库**)是一个不断完善的开放源代码的jsp标签库，是由apache的jakarta小组来维护的。

#### jstl 解决什么问题	
	处理jsp 页面的逻辑处理。比如判断，循环等。
----------------------------------------------------------------------------
#### jstl 如何使用

##### 在jsp页面添加taglib指令才能使用jstl标签
	<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="core"%>
	使用	jstl标签	

##### 语法 `<prefix:命令 test=""></prefix:命令>`
	<core:if test="${num>3}"></core:if>
----------------------------------------------------------------------------
##### 常用标签	
###### 通用标签 set out remove
	设置变量：<core:set var="num" value="${10+5 }" scope="page"></core:set>
	输出数据：<core:out value="${num }"></core:out>
	移除变量：<core:remove var="num"/><br/>
	移除后输出设置默认值： <core:out value="${num }" default="默认值"></core:out>

###### 条件标签 if   choose(switch)
	<%
		pageContext.setAttribute("num", 5);
		pageContext.setAttribute("index", 5);
	%>
* if
	`<core:if test="${num>3}">
		${num}
	</core:if>`
* choose
###### 例
	<core:choose>
		<core:when test="${num==1 }">
		num = 1
	</core:when>
		<core:when test="${num==2 }">
		num=2
	</core:when>
		<core:when test="${num==3 }">
		num=3
	</core:when>
		<core:when test="${num==5 }">
		num =5
	</core:when>
		<core:otherwise>
		默认值
	</core:otherwise>
	</core:choose>



###### 迭代器	 foreach
* 普通循环
	* var 声明变量
	* begin 初始化
	* end 结束条件
	* step 步长
		<core:forEach var="i" begin="1" end="10" step="2">
			${i }

		</core:forEach>

* 循环数据
	* items 获取到集合
	* varStatus 元素的信息
	* var 单个数据
###### 例1
	<%
        List<String> list = new ArrayList<String>();
		list.add("aaa");
		list.add("bbb");
		list.add("cccc");
		pageContext.setAttribute("list", list);

		Map<String, String> map = new HashMap<String, String>();
		map.put("a", "A");
		map.put("b", "B");
		map.put("c", "C");
		map.put("d", "D");
		map.put("e", "E");
		pageContext.setAttribute("map", map);
	%>
	<core:forEach items="${list }" var="l">
		${l }
	</core:forEach>

###### 例2
	<table border="1px">
		<tr>
			<th>key</th>
			<th>内容</th>
			<th>索引</th>
			<th>计数</th>
			<th>是否第一个</th>
			<th>是否最后一个</th>
		</tr>
	<core:forEach items="${map }" var="m" varStatus="vs">

		<tr>
			<td>${m['key'] }</td>
			<td>${m['value'] }</td>
			<td>${vs.index }</td>
			<td>${vs.count }</td>
			<td>${vs.first }</td>
			<td>${vs.last }</td>
		</tr>
	</core:forEach>

	</table>






















