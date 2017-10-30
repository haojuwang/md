#shell
## shell 开头
	#!/bin/bash
## 执行shell脚本的方式
#### 1,bash script-name
	bash test.sh
#### 2,sh script-name	
	sh test.sh
#### 3,source script-name	
	source test.sh
#### 4, . script-name
	. test.sh

####  环境变量
	$HOME  $USER $UID $SHELL $PATH   $PS([\u@\h \W]\$  [root@centos-linux lihao]) 

* env 显示所有的环境变量
#### 设置环境变量	
	vi /etc/profile

	. /etc/profile

	LIHAO = 'i am lihao'
	export LIHAO


	echo $LIHAO

#### 局部变量	
	#!/bin/bash
	a=192.56.98.10
	b='192.56.98.10'
	//=左右不能有空格
	c = "192.56.98.10"
	echo "a=$a"
	echo "b=$b"

	echo "c=${c}"

	输出：
	a=192.56.98.10
	b=192.56.98.10
	c=

##### -
	#!/bin/bash
	a=3-2
	echo $a

	输出： 3-2











