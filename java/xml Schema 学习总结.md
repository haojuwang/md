## xml Schema 学习总结
#### 简单类型（simpleType）
* 1，**简单元素**：指只能包含文本内容，不能够包含子元素，也没有属性的元素。
```
	格式：
		<xs:element name="xxx" type="yyy" />
	例子：
		<xs:element name="name" type="xs:string"/>
		<xs:element name="age" type="xs:integer"/> 
		<xs:element name="gender" type="xs:boolean"/>
```

* 2,**属性**: 所有的元素均被声明简单类型。只有复杂类型的元素才可以拥有属性。
```
	格式:
		<xs:attribute name="xxx" type="yyy"/>

	例子:
	<xs:attribute name="lang" type="xs:string"/>

```
所有的属性默认都是可选的，我们可以通过使用use关键字明确指出是可选或是必需：
```
	<xs:attribute name="lang" type="xs:string" use="optional"/>
	<xs:attribute name="lang" type="xs:string" use="required"/>

```

我们可以通过使用default 或 fixed 为简单类型（简单元素，属性）指定默认值或固定值，
```
	<xs:element name="color" type="xs:string" default="red"/>
	<xs:attribute name="lang" type="xs:string" fixed="CN"/>

```

3，**对简单类型的约束 **

|      约束        |     含义    |
|:  ---------------  :|: -----------:|
| enumeration        |定义允许值的枚举  |
| fractionDigits     |指定最多允许的小数位数(必须大于或等于零) |
|  length            | 精确指定允许的最大字符长度 |
|  maxExclusive            | 指定允许的最大数值,必须小于该值 |
|  maxInclusive            | 指定允许的最大数值,必须小于或等于该值 |
|  maxLength            | 指定允许的最大字符长度(必须大于等于零) |
|  minExclusive            | 指定允许的最小数值,必须大于该值 |
|  minInclusive            | 指定允许的最小数值,必须大于或等于该值 |
|  minLength            | 指定允许的最小字符长度 |
|  pattern            | 指定允许值的模式,类似正则表达式 | 
|  totalDigits            | 精确指定数字个数 |
|  whiteSpace            | 处理空白(保留:preserve;替换: replace;合并:collapse)|

**示例**

```
	test.xsd
----------------------------------------------------------------
	<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
                elementFormDefault="qualified" attributeFormDefault="unqualified"
>

    <xs:attribute name="interest" type="xs:integer"/>
    <xs:group name="myGroup">
        <xs:sequence >
            <xs:element name="name" type="xs:string"></xs:element>
            <xs:element name="birhtday" type="xs:date"></xs:element>

        </xs:sequence>
    </xs:group>

<xs:simpleType name="ageType">

    <xs:restriction base="xs:integer">
        <xs:minInclusive value="30"></xs:minInclusive>
        <xs:maxExclusive value="100"></xs:maxExclusive>
    </xs:restriction>

</xs:simpleType>

    <xs:element name="age" type="ageType"></xs:element>

    <xs:element name="person">
        <xs:complexType>

            <xs:sequence>
                <xs:group ref="myGroup"></xs:group>
                <xs:element ref="age"></xs:element>

            </xs:sequence>
            <xs:attribute ref="interest" use="required" />
        </xs:complexType>



    </xs:element>



</xs:schema >
----------------------------------------------------------------

		test.xml
----------------------------------------------------------------		
		<?xml version="1.0" encoding="UTF-8"?>
<person xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="test2.xsd"
       interest="223"

>

    <name>zhangsan</name>

    <birhtday>2017-09-28</birhtday>
    <age>33</age>

</person>

```

#### 复杂类型（complexType）
	复杂类型指包含其他元素/属性的元素类型。

```
	<xs:element name="message">
		<xs:complexType>
			<xs:sequence>
				<xs:element name="to" type="xs:string"/>
				<xs:element name="from" type="xs:string"/>
				<xs:element name="body" type="xs:string"/>
			</xs:sequence>
		</xs:complexType>
	</xs:element>

```	
注意元素 to,from,body 包含在<xs:sequence></xs:sequence>中,表明 这些元素必须按照定义的顺序出现在你的 XML 文件中。
当然,message 元素也可以包含一个 type 属性,指向我们定义的复杂类型, 象这样:

```
	<xs:element name="message" type="msg"/>

	<xs:complexType name="msg">
		<xs:sequence>
			<xs:element name="to" type="xs:string"/>
			<xs:element name="from" type="xs:string"/> 
			<xs:element name="body" type="xs:string"/> 
		</xs:sequence>
	</xs:complexType>

```
#### 复杂类型（complexType） 和 简单类型（simpleType）区别
复杂类型和简单类型之间最根本的区别就是:
复杂类型的内容中可以包含其他 元素,也可以带有属性(Attribute),但简单类型既不能包含子元素,也不能 带有任何属性。


#### Schema 使用练习

* 1、如何描述空元素,比如:<product prodid="1345" />
因为是空元素,所以不包含子元素,同时由于包含属性,用 attribute 定义。

```
	<xs:element name="product">
		<xs:complexType>
			<xs:attribute name="prodid" type="xs:positiveInteger"/>
		</xs:complexType>
	</xs:element>

也可以这样:
<xs:element name="product" type="productType"/>
<xs:complexType name="productType">
	<xs:attribute name="prodid" type="xs:positiveInteger"/>
</xs:complexType>

```

* 2、如何描述只含有简单内容(文本/属性)的元素,比如:
```
	<message date="2006-06-26">Hi,My Girl!</message>
```
由于只包含简单内容,所以我们在元素内容定义的外面用 simpleContent 指 出,当描述简单内容的时候,我们需要在简单内容里使用 extension 或者 restriction 来描述内容的数据类型。
```
	<xs:element name="message"> 
		<xs:complexType>
			<xs:simpleContent>
				<xs:extension base="xs:string"> 
					<xs:attribute name="date" type="xs:date" /> 
				</xs:extension>
			</xs:simpleContent> 
		</xs:complexType> 
	</xs:element>

```

其中 message 的属性 date 的数据类型为日期(xs:date)。
顺便提一下:XML Schema 中常用的数据类型有:
xs:string 、xs:decimal 、xs:integer 、xs:boolean 、xs:date 、xs:time 等。

#### 复杂类型（complexType）定义元素如何出现。
	定义元素如何出现:包括 all,sequence,choice 这三个。
*1，	all: 默认值。不限制子元素的出现顺序，每个子元素必须出现且只能出现一次。
```
	<xs:element name="person">
		<xs:complexType>
			<xs:all>
				<xs:element name="firstname" type="xs:string"/>
				<xs:element name="lastname" type="xs:string"/> 
			</xs:all>
		</xs:complexType> 
	</xs:element>

```
*2，sequence:子元素在 XML 文件中按照 XML Schema 定义的顺序出现。

*3、choice:两个或多个子元素中仅出现一个。
```
	<xs:element name="gender"> 
		<xs:complexType>
			<xs:choice>
				<xs:element name="male" type="male"/>
				<xs:element name="female" type="female"/> 
			</xs:choice>
		</xs:complexType> 
	</xs:element>
```
**次数限定类,包括 minOccurs 和 maxOccurs,前者指定最少出现次数,后者指定最多出现次数**
```
	<xs:element name="person">
		<xs:complexType>
			<xs:sequence maxOccurs="10" minOccurs="0">
				<xs:element name="full_name" type="xs:string"/> 
				<xs:element name="child_name" type="xs:string" maxOccurs="10" minOccurs="0"/> 
			</xs:sequence>
		</xs:complexType> 
	</xs:element>
```
** 如果元素出现的最大次数无限制,可以使用 maxOccurs="unbounded"。 **

```
	<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           elementFormDefault="qualified" attributeFormDefault="unqualified"
>
    <xs:element name="姓名" type="xs:string"></xs:element>
    <xs:element name="性别">
        <xs:simpleType>
            <xs:restriction base="xs:string">
                <xs:enumeration value="男"></xs:enumeration>
                <xs:enumeration value="女"></xs:enumeration>
            </xs:restriction>
        </xs:simpleType>
    </xs:element>
    <xs:element name="年龄">
        <xs:simpleType>
            <xs:restriction base="xs:int">
                <xs:minInclusive value="0"></xs:minInclusive>
                <xs:maxInclusive value="150"></xs:maxInclusive>
            </xs:restriction>
        </xs:simpleType>
    </xs:element>



    <xs:element name="学生">
        <xs:complexType >
            <xs:sequence>
                <xs:element ref="姓名"></xs:element>
                <xs:element ref="性别"></xs:element>
                <xs:element ref="年龄"></xs:element>
            </xs:sequence>
            <xs:attribute name="学号" type="xs:string" use="required"></xs:attribute>
        </xs:complexType>
    </xs:element>

    <xs:element name="学生名册">
        <xs:complexType>
            <xs:sequence minOccurs="1" maxOccurs="unbounded">
                <xs:element ref="学生"></xs:element>
            </xs:sequence>
        </xs:complexType>
    </xs:element>

</xs:schema>

--------------------------------------

<?xml version="1.0" encoding="UTF-8"?>
<学生名册 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="demo.xsd"
>


    <学生 学号="1">
        <姓名>张三</姓名>
        <性别>男</性别>
        <年龄>120</年龄>

    </学生>
    <学生 学号="2">
        <姓名>李四</姓名>
        <性别>女</性别>
        <年龄>19</年龄>
    </学生>
    <学生 学号="3">
        <姓名>王五</姓名>
        <性别>男</性别>
        <年龄>21</年龄>
    </学生>


</学生名册>

```

#### dom4j 使用
```
	 private static void read() throws DocumentException {
        SAXReader saxReader = new SAXReader();
        Document doc = saxReader.read(new File("candidate.xml"));
        Element root = doc.getRootElement();

        System.out.println(root.getText());
        System.out.println(root.getName());
        System.out.println("----------------------------");

        List<Element> telList = root.elements("PERSON");
        System.out.println("---" + telList.size());
        telList.forEach(element -> {
//            NAME   ADDRESS  TEL  FAX   EMAIL
            String name = element.element("NAME").getText();
            String address = element.element("ADDRESS").getText();
            String tel = element.element("TEL").getText();
            String fax = element.element("FAX").getText();
            String email = element.element("EMAIL").getText();


            String personid = element.attribute("PERSONID").getText();

            System.out.println("personid = " + personid);

            System.out.println("name = " + name + "\t address=" + address + "\t tel=" + tel +
                    "\t fax=" + fax + "\t email=" + email);

        });
    }

--------------------------------------------------------------
       private static void write() throws Exception {
        Document document = DocumentHelper.createDocument();
        Element root = document.addElement("联系人列表");
        root.addAttribute("公司","A 集团");

        Element contactPerson  = root.addElement("联系人");
        contactPerson.addElement("姓名").addText("张胜男");
        contactPerson.addElement("公司").addText("A 公司");
        contactPerson.addElement("电话").addText("(021)5555666");
        Element address = contactPerson.addElement("地址");
        address.addElement("街道").addText("5");
        address.addElement("城市").addText("上海市");
        address.addElement("省份").addText("上海");

        OutputFormat format = OutputFormat.createPrettyPrint();
        format.setIndentSize(4);
        XMLWriter writer = new XMLWriter(System.out,format);
        writer.write(document);

        XMLWriter filewriter = new XMLWriter(new FileOutputStream(new File("test.xml")),format);
        filewriter.write(document);

    }

```


















