scala
## Functions

* => 左边是参数 右边是函数体
```
val addOne = (x: Int) => x + 1
println(addOne(1)) // 2

val add = (x: Int, y: Int) => x + y
println(add(1, 2)) // 3


val getTheAnswer = () => 42
println(getTheAnswer()) // 42

```	


## Methods
	定义： def关键字开始后面跟着方法名 参数 返回值 方法体。

```
def add(x: Int, y: Int): Int = x + y
println(add(1, 2)) // 3

```	 	

* 接收多个参数，方法的最后一行就是方法的返回值
```
 println(addThrenMultiply(2,3)(2))


 def addThrenMultiply(x: Int,y: Int)(multiplier: Int) :Int = {
    (x+y)*multiplier
  }
```


## Classes
	class 关键字后面跟着类名和构造方法参数。
```
 class Greeter(prefix: String, suffix: String) {
    def greet(name: String): Unit = {
      println(prefix + name + suffix)
    }
  }

  val greeter =  new Greeter("hello,"," !");
    greeter.greet("lihao") //hello,lihao!

```	

## Case Classes
	case class 关键字定义的类是不可变的常用于比较值
```

 case class Point(x: Int,y: Int)





if (point == anotherPoint) {
  println(point + " and " + anotherPoint + " are the same.")
} else {
  println(point + " and " + anotherPoint + " are different.")
}
// Point(1,2) and Point(1,2) are the same.

if (point == yetAnotherPoint) {
  println(point + " and " + yetAnotherPoint + " are the same.")
} else {
  println(point + " and " + yetAnotherPoint + " are different.")
}
// Point(1,2) and Point(2,2) are different.


```	

## Objects 
	object关键字定义的类 是单例










