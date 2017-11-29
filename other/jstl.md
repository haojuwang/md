## jstl 基本使用

### EL表达式的操作符

| 操作符          |                  功能和作用                   |
| ------------ | :--------------------------------------: |
| .            |          访问一个bean属性或者Map entry           |
| []           |               访问一个数组或者链表元素               |
| ()           |             对子表达式分组，用来改变赋值顺序             |
| ? :          |                   条件语句                   |
| + - * / % == |                                          |
| empty        | 用来对一个空变量进行判断：null ,一个空String,空数组 ,空map,没有条目的Collection集合 |
|              |                                          |



### jstl 常用标签库

| 标签库       |                  URI                   | 前缀   |
| --------- | :------------------------------------: | ---- |
| core      |   http://java.sun.com/jsp/jstl/core    | c    |
| functions | http://java.sun.com/jsp/jstl/functions | fn   |

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```



* core 标签库，又被称为核心标签库，该标签库的工作是对于jsp页面一般处理的封装。在该标签库中的标签一共有14个被分为了四个类：

  ```
  多用途核心标签： <c:out>	<c:set>  <c:remove>      <c:catch>
  条件控制标签：  <c:if>		<c:choose>   <c:when>   <c:otherwise>
  循环控制标签：  <c:forEach>   <c:forTokens>
  URL 相关标签： <c:import> 	<c:url>		<c:redirect>  <c:param>
  ```

  ​



#### <c:out> 显示

* <c:out> 标签是最常用的标签，用于在jsp中显示数据，

  ​

| 属性        |                    描述                    |
| --------- | :--------------------------------------: |
| value     |         输出到页面的数据，可以是EL表达式或常量（必须）         |
| default   |          当value为null时显示的数据（可选）           |
| escapeXml | 当设置为 true 时会主动更换特殊字符,比如“&lt;,&gt;,&amp;  （可选默认为true） |
|           |                                          |

```
<c:out value="<h1>out</h1>" default="no-value" escapeXml="false"></c:out>
```

escapeXml 为false 会解析html,为true html代码原样输出



#### <c:set> 赋值

* <c:set>  标签用于变量或javaBean 中的变量属性赋值的工作

  ​

  | 属性       | 描述                                       |
  | -------- | ---------------------------------------- |
  | value    | 值的信息，可以是EL表达式或常量                         |
  | target   | 被赋值的javabean实例的名称，或存在该属性则必须存在property 属性（可选） |
  | property | javaBean 实例的变量属性名称（可选）                   |
  | var      | 被赋值的变量名（可选）                              |
  | scope    | 变量的作用范围，若没有指定，默认为page(可选)                |



```jsp
  <c:set value="this is set" var="set"></c:set>
    ${set}----><c:out value="${set}"></c:out>
```



#### <c:if> 判断标签

* <c:if>标签用于简单的条件语句

  | 属性    | 描述                                       |
  | ----- | ---------------------------------------- |
  | test  | 需要判断的提交                                  |
  | var   | 保存判断结果true 或false 的变量名，该变量可以供之后的工作使用（可选） |
  | scope | 变量的作用范围，若没有指定，默认为page(可选)                |

```jsp
  <c:set value="lihao" var="name"></c:set>
    <c:if test="${name == 'lihao'}" var="result">
        this is lihao
    </c:if>

    ${result}
```





#### <c:choose>   <c:when>   <c:otherwise> 复杂判断

* <c:choose> 标签没有属性，可以被认为是父标签<c:when> <c:otherwise>  将作为其子标签来使用。
* <c:when> 标签等价于“if” 语句，他包含一个test属性，该属性表示需要判断的提交。
* <c:otherwise> 标签没有属性等价于“else ” 语句。

```jsp
  <c:choose>
        <c:when test="${param.when == 1}">
            1
        </c:when>
        <c:when test="${param.when == 2}">
            2
        </c:when>
        <c:otherwise>
            defalut
        </c:otherwise>
    </c:choose>
```





#### <c:forEach> 循环

| 属性        | 描述                                      |
| --------- | --------------------------------------- |
| items     | 进行循环的集合（可选）                             |
| begin     | 开始条件（可选）                                |
| end       | 结束条件（可选）                                |
| step      | 循环步长，默认为1(可选)                           |
| var       | 做循环的对象变量名，若存在items属性，则表示循环集合中对象的变量名(可选) |
| varStatus | 显示循环状态的变量(可选)                           |

```jsp
    <c:forEach items="${requestScope.list}" var="list" varStatus="vs">
        ${vs.index} ---> ${list}
    </c:forEach>
    <hr/>
    
    <c:forEach var="i" begin="1" end="10" step="1">
        ${i} -->
    </c:forEach>
```





#### <c:forTokens> 分割字符串

* <c:forTokens> 标签可以根据某个分隔符分割指定字符串，相当于java.util.StringTokenizer类

  | 属性        | 描述             |
  | --------- | -------------- |
  | items     | 进行分割的EL表达式或常量  |
  | delims    | 分隔符            |
  | begin     | 开始条件（可选）       |
  | end       | 结束条件（可选）       |
  | step      | 循环的步长，默认为1（可选） |
  | var       | 做循环的对象变量名（可选）  |
  | varStatus | x显示循环状态的变量(可选) |

  ​

```jsp
  <c:forTokens items="aa,bb,cc,dd,ee,ff" delims="," begin="0" step="2" var="value" varStatus="vs">
      ${vs.index}  -->  ${value}
    </c:forTokens>
```





#### <c:import> 导入

* <c:import> 标签允许包含另一个jsp 页面到本页面来

  | 属性           | 描述                                       |
  | ------------ | ---------------------------------------- |
  | url          | 需要导入页面的url                               |
  | context      | Web Context 该属性用于在不同的 Context 下导入页面,当出现 context 属性时,必须以“/ ”开头,此时也需要 url 属性以“ / ”开头(可选) |
  | charEncoding | 导入页面的字符集（可选）                             |
  | var          | 可以定义导入文本的变量名（可选）                         |
  | scope        | 导入文本的变量名作用范围(可选）                         |
  | varReader    | 接受文本的 java.io.Reader 类变量名(可选)            |

  ​

```
<c:import url="import.jsp" ></c:import>
```





#### <c:url> 地址

* <c:url> 标签用于得到一个url 地址

  | 属性           | 描述                                       |
  | ------------ | ---------------------------------------- |
  | value        | 页面的URL 地址                                |
  | context      | web context 该属性用于得到不同Context下的URL 地址，当出现context 属性时，必须以“/”开头，此时也需要url属性以"/"开头（可选） |
  | charEncoding | URL 的字符集（可选）                             |
  | var          | 存储URL 的变量名（可选）                           |
  | scope        | 变量名作用范围（可选）                              |

  ​

```jsp
  <c:url value="/import.jsp" context="${pageContext.request.contextPath}" var="urlPage"></c:url>
    <a href="${urlPage}" target="_blank">link</a>
```





#### <c:redirect>  用于页面的重定向

* ```
  <c:redirect> 用于页面的重定向,该标签的作用相当于 response.setRedirect 方法的工作。它包含 url 和 context 两个属性

  <c:redirect url="/MyHtml.html"/>
  该示例若出现在 JSP 中,则将重定向到当前 Web Context 下的“ MyHtml.html ”页面,一般会与 <c:if> 等标签一起使用。
  ```



#### <c:param>  传递参数

* <c:param> 用来为包含或重定向的页面传递参数

  ```
  <c:redirect url="/MyHtml.jsp">
  ￼￼	<c:param name="userName" value=”RW” />
  </c:redirect>
  ```

  ​



##  Functions 标签库



* Functions 标签库分为两大类，共16个函数

  ```
  长度函数： fn:length

  字符串处理函数： fn:contains    fn:containsIgnoreCoase     fn:endsWith    fn:escapeXml   fn:indexOf   fn:join       fn:replace    fn:replace   fn:split    fn:startsWith     fn:substring      fn:substringAfter       fn:substringBefore      fn:toLowerCace   fn:toUperCase     fn:trim
  ```

#### 长度函数fn:length

* 长度函数 fn:length 的出现有重要意义。在jstl1.0，有一个功能被忽略了，那就是对集合的长度取值。虽然 java.utl.Collection接口定义了size方法，但是该方法不是一个标准的javaBean属性方法（没有 get set方法），因此无法通过EL 表达式“${collection.size}” 来轻松取得。

  ```jsp
    <%
          ArrayList<String> list = new ArrayList<String>();
          list.add("aa");
          list.add("bb");
          list.add("cc");
          list.add("dd");

          request.setAttribute("list",list);

      %>
      
      
         ${fn:length(requestScope.list)}
  ```

  ​

#### fn:contains 判断函数

* fn:contains 函数用来判断源字符串是否包含子字符串。它包括string和substring 两个参数，它们都是String 类型，分别表示源字符串和子字符串。其中返回结果为一个boolean类型的值

```
 ${fn:contains("abc","A" )} //false
    --->
    ${fn:containsIgnoreCase("abc","A" )} //true
```



#### fn:startsWidth 词头判断函数

fn:startsWith 函数用来判断源字符串是否符合一连串的特定词头。它除了包含一个 string 参数外

还包含一个 subffx 参数,表示词头字符串,同样是 String 类型。该函数返回一个 boolean 类型
的值。

```

    ${fn:startsWith("abc", "d")}   

    ${fn:startsWith("abc", "ab")}

```

​	

#### fn:endsWidth



#### fn:escapeXml

* fn:escapeXml 函数用于将所有特殊字符转化为字符实体码。它只包含一个 string 参数,返回一个 String 类型的值。

```
    ${fn:escapeXml("<h1>fn:escapeXml<h1>")}
    输出：<h1>fn:escapeXml<h1>
```



#### fn:indexOf 字符匹配函数

fn:indexOf 函数用于取得子字符串与源字符串匹配的开始位置，若子字符串与源字符串中的内容没有匹配成功将返回“-1”

```
${fn:indexOf("abc", "c")}
       ----->
${fn:indexOf("abc", "d")}
    
2 -----> -1    
```



#### fn:join 连接字符串

* fn:join 函数允许为一个字符串数组中的每一个字符串加上分隔符,并连接起来。
* 第一个参数是字符串数组。其类型必须为String[] 类型
* seperator 分隔符，其类型必须为String 类型。

```
 <%
        String[] stringArray = {"a","b","c"};
        request.setAttribute("stringArray",stringArray);
    %>


    ${fn:join(requestScope.stringArray,"---" )}
    
    
    a---b---c
```



#### fn:replace 替换函数



#### fn:split 分割符转换数组



#### fn:substring 字符串截取函数



#### fn:substringBefore 函数 起始到定位截取字符



#### fn:toLowerCase 小写转换函数



#### fn:toUpperCase 函数 大写转换函数



#### 空格删除函数 fn:trim













​			
​		
​	