strut2笔记2.md
## result 结果集
	<result>元素的type属性用来指定结果类型！如果没有指定type时，默认为dispatcher，下面介绍5种常用的结果类型。

##### 1，dispathcer:默认值： 表示转发到页面
	<result name="success" type="dispatcher">
     <param name="location">foo.jsp</param>
   </result>
##### 2，stream：表示转发到另一个Action，所以这时<result>元素的内容需要是另一个<action>的名字；
	 <result name="download" type="stream">
        <param name="contentType">image/png</param>
        <param name="inputName">avatar</param>
        <param name="contentDisposition">attachment;filename="${uploadName}"</param>
        <param name="bufferSize">10240</param>
    </result>

##### 3，redirect：表示重定向到页面
	    The redirect URL generated will be:
    foo.jsp#FRAGMENT
    <result name="success" type="redirect">
    	<param name="location">foo.jsp</param>
     	<param name="parse">false</param>
     	<param name="anchor">FRAGMENT</param>
    </result>
    <result name="home" type="redirect">/login/home.jsp</result>
##### 4，redirectAction：表示重定向到另一个Action。
	<result name="rlist" type="redirectAction">
        <param name="actionName">userAction_list</param>
        <param name="namespace">/p</param>
    </result>


##### 5，textplain:表示将以文本的形式发送资源。
	<action name="displayJspRawContent" >
    	<result type="plainText">/myJspFile.jsp</result>
    </action>  
    <action name="displayJspRawContent" >
	    <result type="plainText">
	    	<param name="location">/myJspFile.jsp</param>
	    	<param name="charSet">UTF-8</param>
	    </result>
    </action>

## 封装请求参数
#### 属性封装
	只要在Action 中提供与参数对应的get/set方法即可自动封装。
	public class ParamseAction extends ActionSupport {
	    private String username;

	    public String getUsername() {
	        return username;
	    }

	    public void setUsername(String username) {
	        this.username = username;
	    }

	    @Override
	    public String execute() throws Exception {
	        System.out.println("username = "+username);
	        return SUCCESS;
	    }
	}


## 类型转换
#### 默认支持类型转换。
* 字符串 -> 指定类型 之间转换
	字符串 转成 指定类型：表单提交，浏览器发送服务器
	指定类型 转成 字符串；标签回显，服务器发送浏览器

### 自定义类型转换器
#### 实现类
	方案1：实现接口：TypeConverter，有一个方法，但参数过多。
	方法2：继承默认实现类：DefaultTypeConverter 。提供简洁方法convertValue(Object , Class)

	convertValue(Object value ,  Class toType)
	#1 表单提交，浏览器发送到服务器。浏览器发送的肯定字符串String，需要转换成指定的类型。例如Date类型
	 参数1：value，表示浏览器发送的数据。类型是String[] ,底层使用request.getParameterValues("...")
	 参数2：toType，表示需要转换的类型，java.uilt.Date类型
	 ** 操作
	 // 如果toType是 Date类型，表示希望将 字符串转成 时间
	  if(toType == java.util.Date.class){
	      //获得数据
	      String[] params = (String[])value;
	     //转成成时间
	  }

	#2 标签回显，服务器发送 浏览器，类型之前已经从字符串转成时间，现在希望将时间再转换成 字符串。
	参数1：value，表示服务器已经转成好的时间。类型Date。
	参数2：toType，表示需要转换的类型，String类型
	  ** 操作
	  if(toType == String.class){
	    // 将数据强转时间
	    Date date = (Date)value;
	    // 格式化
	  }

	  public class MyDateConverter extends DefaultTypeConverter {
	    @Override
	    public Object convertValue(Object value, Class toType) {
	        System.out.print("正在转换");
	        SimpleDateFormat sb = new SimpleDateFormat("yyyy/MM/dd");
	        try {
	        //String——>date
	        if(toType == Date.class) {
	            String[] params = (String[]) value;

	            return sb.parse(params[0]);

	        }
	        //date->String
	        if(toType == String.class) {
	            Date date = (Date) value;
	            return sb.format(date);
	        }


	        } catch (ParseException e) {
	            e.printStackTrace();
	        }

	       throw new RuntimeException("参数错误");
	    }
	}

##### 配置文件
	位置：action类同包
	名称：actionClass-conversion.properties
	例：StrutsFormAction-conversion.properties
	birthday=cn.structs.h_parmase.MyDateConverter

## 数据校验
	实现接口，并编写方法 validate方法() , 此处“方法”表示执行的方法名称，首字母大写。
	例如：add() 执行前需要校验，必须编写 validateAdd()
	validateLogin（）

#### 提示错误
	addFieldError("", "") 给指定的字段设置提示信息
		<s:fielderror>jsp显示错误
	addActionMessage(aMessage) action提示提示信息
		<s:actionmessage/>jsp显示错误
	addActionError(anErrorMessage) action错误
		<s:actionerror/>jsp显示错误

	public void validateLogin() {
        clearFieldErrors();
        if (StringUtils.isEmpty(user.getLoginname())) {
            addFieldError("loginname", "用户名不能为空");
        }

        if (StringUtils.isEmpty(user.getLoginpass())) {
            addFieldError("loginpass", "密码不能为空");
        }
    }

## 拦截器
#### 拦截器介绍
	struts提供拦截器，对action类进行增强的。struts已经实现多个拦截器，完成不同的功能。
	例如：文件上传、数据校验、类型转换、参数封装等.

#### 默认拦截者栈
	truts-default.xml 提供struts完成所有拦截器，也提供默认拦截器栈
	<default-interceptor-ref name="defaultStack"/> 所有的action默认使用那个拦截器栈
	<interceptor-stack name="defaultStack"> 	声明一个拦截器栈，名称为“defaultStack”，通常称为：默认拦截器栈

* servletConfig : 用于给action类注入Servlet api。
	例如：action类实现ServletRequestAware就可以被struts框架注入HttpServletRequest对象
* modelDriven： 调用action类的getModel()方法，获得javabean实例，如果没为null，将交予struts。
* fileUpload：struts默认支持文件上传。
* params：给action类进行数据封装。如果使用ModelDriven，就给javabean封装数据。
* conversionError：将转换错误添加到action类的错误提示信息中。
	将执行 action.addFieldError("属性","错误提示");
* validation：将执行action所有校验。先执行注解校验，再执行单个方法校验，最后所有方法的校验
* workflow ：从action类获得添加的错误信息，如果没有发行。如果有返回“input”

#### 自定义拦截器
* 实现接口：com.opensymphony.xwork2.interceptor.Interceptor。
* 继承父类：com.opensymphony.xwork2.interceptor.MethodFilterInterceptor。
	在使用自定义拦截器，可以对指定的方法进行操作（哪些方法不拦截，哪些必须拦截）
		设置属性includeMethods，确定哪些方法进行拦截
		设置属性excludeMethods，确定哪些方法不进行拦截
*	<default-interceptor-ref name="xxx">将指定的拦截器，声明成默认的。
	注意：如果使用自定义xxx，“defaultStack”将被覆盖。

* 注意：拦截器只拦截action类，不拦截jsp文件。

#### 示例 是否登陆拦截器
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE struts PUBLIC
	        "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	        "http://struts.apache.org/dtds/struts-2.3.dtd">

	<struts>

	    <package name="project" namespace="/p" extends="struts-default">
	        <interceptors>
	            <interceptor name="login" class="cn.project.action.LoginInterceptor"></interceptor>

	            <interceptor-stack name="myDefaultStarck">
	                <interceptor-ref name="login">
	                    <param name="excludeMethods">login</param>
	                </interceptor-ref>
	                <interceptor-ref name="defaultStack"></interceptor-ref>
	            </interceptor-stack>
	        </interceptors>

	        <default-interceptor-ref name="myDefaultStarck"></default-interceptor-ref>

	        <!--配置全局错误-->
	        <global-exception-mappings>
	            <exception-mapping exception="cn.project.exception.UserException" result="error"></exception-mapping>
	        </global-exception-mappings>
	        <action name="userAction_*" class="cn.project.action.UserAction" method="{1}">
	            <result name="error">/user/error1.jsp</result>
	            <result name="login">/login/login.jsp</result>
	            <result name="input">/login/login.jsp</result>
	            <result name="list">/user/list.jsp</result>
	            <result name="view">/user/view.jsp</result>
	            <result name="edit">/user/edit.jsp</result>

	            <result name="rlist" type="redirectAction">
	                <param name="actionName">userAction_list</param>
	                <param name="namespace">/p</param>
	            </result>
	            <result name="download" type="stream">
	                <param name="contentType">image/png</param>
	                <param name="inputName">avatar</param>
	                <param name="contentDisposition">attachment;filename="${uploadName}"</param>
	                <param name="bufferSize">10240</param>
	            </result>


	            <result name="home" type="redirect">/login/home.jsp</result>

	        </action>
	       

	    </package>

	</struts>

	public class LoginInterceptor extends MethodFilterInterceptor {
	    @Override
	    protected String doIntercept(ActionInvocation invocation) throws Exception {
	        Map<String, Object> sessionScope = ActionContext.getContext().getSession();
	        ProjectUser user = (ProjectUser) sessionScope.get("user");

	        if(user == null) {
	            return "login";
	        }
	        return invocation.invoke();
	    }
	}









































