## 输入校验
#### validate,validatexxx
* 使用addFieldError(fieldName,errorMessage)方法存入字段的错误信息。
* 使用addActionError(aanErrorMessage)方法中存入action的错误信息。
* 使用addActionMessage(aMessage)方法存入action提示消息。
* 使用<s:filederror filedName="xxxx"/>取出字段的错误消息。
* 使用<s:actionerror />取出action的错误消息。
* 使用<s:actionmessage />取出action提示消息。
* 使用struts2 提供的表单标签会自动显示字段的错误消息。

## OGNL表达式
#### 什么是OGNL语言
	OGNL的全称为Object Graphic Navigation Language（对象图导航语言）。它是Struts2的默认表达式语言！
	使用OGNL需要导入OGNL的Jar包：ognl-3.0.5.jar

#### OGNL 的功能介绍
* EL 一样的javaBean导航
* 调用对象方法
* 调用静态方法
* 索引数组元素
* 操作集合

##### 取值语法
	public static Object getValue(@NotNull String expression,
                                        Map context,
                                        Object root)
                                throws ognl.OgnlException

*  expression ongl表达式。
*  context(map)    上下文。
*  root(list/map/bean)  root 根


##### 从root根 bean 中取值
	User u1 = new User();
    u1.setUsername("tom");
    String username = (String) Ognl.getValue("username", new HashMap(), u1);
    System.out.println(username);
    ----------------
    输出：tom

##### 从root根 list 中取值 "[第几个].属性"
	ArrayList<User> list = new ArrayList<User>();
    User u1 = new User();
    u1.setUsername("tom
    User u2 = new User();
    u2.setUsername("jerry
    list.add(u1);
    list.add(u2)
    String username = (String) Ognl.getValue("[1].username", new HashMap(), list);
    System.out.println(username);
    ----------------
    输出：jerry

##### 从root根引用值中取值 “引用属性.属性”    
    User user = new User();
    user.setUsername("user");

    Address address = new Address();
    address.setCity("北京");

    user.setAddress(address);

    String city = (String) Ognl.getValue("address.city", new HashMap(), user);

    System.out.println("city : " + city);
    ----------------
    输出： city : 北京


##### 从context上下文中取值"#属性名称"
	Map<String, Object> context = new HashMap<String, Object>();
    context.put("username", "context");
    User user = new User();
    user.setUsername("user");
    //# 表示取值 context
    String mapUsername = (String) Ognl.getValue("#username", context, user);
    String rootUsername = (String) Ognl.getValue("username", context, user);
    System.out.println("mapUsername = " + mapUsername+"\t"+"rootUsername="+rootUsername);
    ----------------
    输出：mapUsername = context	rootUsername=user

##### 给root根属性赋值
	User user = new User();
    Ognl.getValue("username='tom'", new HashMap(), user);
    System.out.println("username : " + user.getUsername());
    ----------------
    输出：username : tom

 	User user = new User();
    Ognl.setValue("username", new HashMap(), user, "jerry");
    System.out.println(user.getUsername());
 	----------------
    输出：jerry

    User user = new User();
    Ognl.getValue("setUsername('jack')", new HashMap(), user);
    System.out.println(user.getUsername());
    ----------------
    输出：jack

##### 获取静态值 “@全限定名@静态方法”
	String time = (String) Ognl.getValue("@cn.structs.k_orgl.DateUtils@getTime()", new HashMap(), new Object());
    System.out.println("time = " + time);
 	----------------
    输出：time = 2016-12-28 14:43:10

##### 数学运算
	int result = (int) Ognl.getValue("1+1", new HashMap(), new Object());
    System.out.println("result =" + result);
	----------------
    输出：result =2

##### 创建list对象
	List list = (List) Ognl.getValue("{'tom','jerry','jack','rose'}", new HashMap(), new 	Object());
    System.out.println(list);
    ----------------
    输出：[tom, jerry, jack, rose]

##### 创建map对象
	Map map = (Map) Ognl.getValue("#{'name':'tom','age':'18'}",new HashMap(),new Object());
    System.out.println(map);
	----------------
    输出：{name=tom, age=18}


### OGNL 与 Struts2 的结合
#### Struts2中OGNL操作的上下文和根对象

* 上下文对象是ActionContext.
* 根对象是ValueStack，它是一个栈结构，提供了压栈和谈栈等方法，通常栈顶元素是
	当前方法的Action对象。
* 每个请求都有自己的ActionContext,每个ActionContext中都有自己的ValueStack。

Struts2中的上下文对象就是ActionContext，而根对象是ValueStact对象。但
struts2中根对象ValueStack很特殊，他不是一个对象，而是一组对象（底层是ArrayList的栈结构）。
可以通过ActionContext来获取ValueStack:ActionContext.getContext().getValueStack();
也可以从request中获取ValueStack：request.getAttribute("struts.valueStack").

##### ActionContext 内部的数据：
* parameters：Map类型，对应HttpServletRequest中的请求参数；
* request：Map类型，对应HttpServletRequest中的属性；
* session：Map类型，对应HttpSession中的属性；
* application：Map类型，对应ServletContext中的属性；
* attr：Map类型，对应request、session、application三个域，即全域查询的Map；

##### ValueStack内部的数据：
* Action对象；
* 模型对象（ModelDriven）;
不同的请求对应不同的ValueStack,通过在请求一个Action时，都会把当前Action的引用放到
Valuestact中。一般情况下，当前Action放在ValueStack的栈顶。










































