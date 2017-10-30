RPC框架 之 Apach thrift.md

## Thrift
* 1，Apache Thrift 主要用于各个服务之间的RPC通信，支持跨语言，常用语言：C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, OCaml and Delphi都支持。

* 2，Thrift 是一个典型的CS(客户端/服务器)结构，客户端和服务端可以使用不同的语言开发，既然客户端和服务端都能使用不同的语言开发，那么一定就要有一种中间语言来关联客户端和服务端的语言，这种语言就是IDL(Interface Description Language), .thrift 就是一种IDL语言


#### Trift 数据类型
	Trift 不支持无符号类型，因为很多编程语言不存在无符号类型，比如java,php

##### 基础数据类型

* byte: 有符号字节。

* i16: 16位有符号整数。

* i32：32位有符号整数。

* i64:64位有符号整数。

* double: 64位 浮点数。

* string: 字符串。


thrift --gen <language> <Thrift filename>
