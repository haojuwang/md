# ajax

## ajax 是什么
	是一种用于创建更好更快以及交互性更强的web引用程序的技术，是基于javascript，xml html css新用法

## ajax 有什么用
	Ajax 只刷新局部页面的技术，异步验证数据或者异步请求服务器拿到需要的数据
* javascript: 更新局部的网页
* xml: 一般用于请求数据和响应数据的封装	
* XMLHttpRequest 对象：发送请求到服务器并获得返回结果
* css 美化页面样式
* 异步：发送请求后不等返回结果，由回调函数处理结果

## ajax 如何用
* 创建XMLHttpRequest 对象
	var xmlhttp;
	if (window.XMLHttpRequest) {// code for IE7+, Firefox, Chrome, Opera, Safari
		xmlhttp = new XMLHttpRequest();
	} else {// code for IE6, IE5
		xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
	}

* 方法：
	open() "get|post" ,"url"
	send() 发送请求，可以带参数或null
	setRequestHeader()	
* 属性：
	readyState： 类型short 只读
	responseText： 类型	String;只读
	responseXML：类型short 只读

#### 常用方法

|方法名				|            说明 |
|-----------------------|:--------------------------:|
|open(method,url,aync)|建立与服务器的连接  method参数指定请求的HTTP方法,典型的值是GET或POST  URL参数指请求的地址   async参数指定是否使用异步请求，其值为true或false|	
|send(content)|发送请求content 参数指定请求的参数|
|setRequestHeader(header,vallue)| 设置请求的头信息|


#### 常用属性
* onredystatechange: 指定回调函数
* readyState:XMLHttpRequest的状态信息

|就绪状态码				|            说明 |
|-----------------------|:--------------------------:|
|0| XMLHttpRequest对象没有完成初始化 即：刚刚创建。|
|1| XMLHttpRequest对象开始发送请求 调用了open方法，但还没有调用send方法。请求还没有发出|
|2| XMLHttpRequest对象的请求发送完成  send方法已经调用，数据已经提交到服务器，但没有任何响应|
|3| XMLHttpRequest对象开始读取响应，还没有结束  收到了所有的响应消息头，但正文还没有完全收到|
|4| XMLHttpRequest对象读取响应结束 一切都收到了|

* status：HTTP的状态码

|状态码				|            说明 |
|-----------------------|:--------------------------:|
|200| 服务器响应正常|
|400| 无法找到请求的资源|
|403| 没有访问权限|
|404| 访问的资源不存在|
|500| 服务器内部错误|

** 就绪状态是4而且状态码是200，才可以处理服务器数据 **


#### 代码示例
	//获取XMLHttpRequest对象
	function getXMLHttpRequest() {
		var xmlhttp;
		if (window.XMLHttpRequest) {// code for IE7+, Firefox, Chrome, Opera, Safari
			xmlhttp = new XMLHttpRequest();
		} else {// code for IE6, IE5
			xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
		}
		return xmlhttp;
	}

	var usernameInput = document.getElementById("username");
	var msgSpan = document.getElementById("msg");
	usernameInput.onblur = function() {
	xmlhttp = getXMLHttpRequest();
	var username = usernameInput.value;
	//监听状态
	xmlhttp.onreadystatechange = function() {
		//响应状态 4代表响应成功
		if(xmlhttp.readyState == 4) {
			//判断服务器响应是否正常 200一切正常
			if(xmlhttp.status == 200){
				if(xmlhttp.responseText == "true"){
					msgSpan.innerHTML ="可以注册"
				} else {
					msgSpan.innerHTML ="不可以注册"
				}
			}
		}
	}
	
	//创建链接
	xmlhttp.open("get","${pageContext.request.contextPath}/servlet/checkNameServlet?name="+username);
	//发送请求
	xmlhttp.send(null);









