## jstl 基本使用

### EL表达式的操作符

| 操作符          |                  功能和作用                   |
| ------------ | :--------------------------------------: |
| .            |          访问一个bean属性或者Map entry           |
| []           |             访问一个数组或者lianbiao             |
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



#### <c:out>

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

