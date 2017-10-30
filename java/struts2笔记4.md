## Strtus2 OGNL 表达式的结合
	描述：struts2 为OGNL表达式准备了两个对象
			ActionContext:最为ognl表达式的Context
			ValueStack:最为ognl表达式的Root
		以上两个对象的创建都是StrutsPrepareAndExecuteFilter中准备好的

#### ognl和Struts使用上结合：
##### 表单提交，其中提交的键可以看作是ognl表达式
	Action中有User对象，我们想直接将表单参数提交到User对象中封装做法：
		1，提交的参数的键=>user.name就会在值栈中查找名为user的对象，并赋值到该对象的name属性。
		2，使用ModelDriven，我们的Action在getModel方法中将User的对象返回，ModelDriven拦截器
			会将我们返回的User对象放入之战中（栈顶），那么表单中直接提交name,将会把name值装入栈顶的user对象的name属性。		

##### 文件下载中文件乱码问题解决
	<result name="success" type="stream" >
		   <param name="contentType">application/zip</param>
		   <param name="inputName">zip</param>
		   <!-- 
		   	1.相应头中只能使用latin(Iso-8859-1)码表
		   	2.使用URLEncode编码,对中文进行编码才能发送  => %E6%F7
			3. 在struts.xml配置文件中可以使用ognl表达式=> ${} 在扩种书写ognl(注意:虽然跟el表达式格式看似相同,但是就是ognl不是el.)
		    -->
		   <param name="contentDisposition">attachment;filename="${fileName}"</param>
		   <param name="bufferSize">10240</param>
	</result>
-----------------------------------------------------------------------
	public class FileDownAction extends ActionSupport {
	   private String fileName;
	   public InputStream getZip() {
	       ServletContext sc = ServletActionContext.getServletContext();
	      return sc.getResourceAsStream("/WEB-INF/shanping.zip");
	   }

	    public void setFileName(String fileName) {
	        this.fileName = fileName;
	    }

	    /**
	     * 获取下载显示的文件名称
	     *
	     * @return
	     */
	    public String getDownloadFileName() {
	        String downFileName = fileName;
	        try {
	            downFileName = new String(downFileName.getBytes(), "ISO8859-1");
	        } catch (UnsupportedEncodingException e) {
	//            log.error("获取下载显示的文件名称",e);
	        }
	        System.out.println("文件名称:"+downFileName);
	        return downFileName;
	    }


	    public String excute(){
	        return SUCCESS;
	    }
	}

##### <s:debug> 标签
##### Struts2标签中OGNL表达式（<s:properties>）

## Struts2 标签
	标签分为两个部分 “通用标签”和“ui 标签”两大部分




















































