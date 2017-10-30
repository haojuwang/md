
xml描述的是结构和语义，而不是格式
1,eXtensible Markup Language (可扩展的标记语言)。
2, Document Type Definition (DTD,文档类型定义)。
3,xml 中的每个元素都是成对出现的（有开始，有结束）<student></student>,
  xml 中的元素嵌套关系要保持正确性，即先开始的标记要先结束，后开始的标记要后结束。
4，每一个xml文档都有且只有一个跟元素（Root Element）。所谓根元素，就是唯一一个包含了
其他元素的元素。
5,** XML 描述的是文档的内容与语义 **  ,而不是文档应当如何显示。
6, 使用 XML 可以实现异构语言、异构平台之间的交互。


#### xml 文档声明
第一行是xml 声明
<?xml version="1.0" encoding="utf-8" ?>
* 对于 XML 文档来说,<?处理指令必须要顶格写,前面不能有任何的空白。
*  XML 元素可以具有属性,属性的形式为:
	属性名=”属性值”,比如 gender=”male”
 	属性值需要使用单引号或双引号括起来。多个属性之间使用空格分开。
* xml的注释：<!-- comments -->
* 在一个元素上，相同的属性只能出现一次。

#### 实体
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE  company[
<!ENTITY shengsiyuan "省四院">
<!ENTITY address "北京">
]>


<company>
    <name>&shengsiyuan;</name>
    <address>&address;</address>
</company>
```
*PCDATA 用于元素  CDATA用于属性

##### 关于普通实体与参数实体
*1，普通实体是指在dtd中定义，xml 中使用，使用的时候格式为：&address;
*2,参数实体是指在dtd中定义，dtd中使用，定义的时候使用%，%address;
*3,外部普通实体的定义方式是:
<!ENTITY address SYSTEM “http://www.shengsiyuan.com/xml.xml”>
表 示 使 用 http://www.shengsiyuan.com/xml.xml 网 址 的 内 容 来 替 换 掉 address,而不是使用 http://www.shengsiyuan.com/xml.xml 字符串本身来 替换掉 address
*4,外部参数实体:
<!ENTITY % address SYSTEM “http://www.shengsiyuan.com/xml.xml”>,使用 的时候:%address;

