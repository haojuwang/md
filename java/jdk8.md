## jdk8



#### Lambda表达式的基本结构：
	（param1,param2）->{
		
	}
	（type1 param1,type2 param2）->{
		body
	}
	箭头左边是抽象方法的参数  右边是抽象方法实现体
* 一个Lambda表达式可以有零个或多个参数。
* 参数的类型既可以明确声明，也可以根据上下文来推断。例如：(int a) 与 (a)效果相同
* 所有参数需要包含在圆括号内，参数之间用逗号相隔。例如（a,b）或(int a，String b)
* 圆括号代表参数集为空。例如（）->42.
* 当只有一个参数，且其类型可推导时，圆括号（）可省略。例如 a -> return a*a.
* Lambda 表达式的主体可包含零条或多条语句。
* 如果Lambda表达式的主体只有一条语句，花括号{}可省略。匿名函数的返回类型与该主题表达式一致。
* 如果Lambda表达式的主题包含一条以上语句，则表达式必须包含在花括号{}中。
	匿名函数的返回值类型与代码块返回类型一致，若没有返回值则为空。


#### Lambda表达式作用
##### 1.Lambda 表达式为java 添加了缺失的函数式编程特性，是我们能将函数当做一等公民看待。
##### 2.在函数作为一等公民的语言中，Lambda 表达式的类型是函数。但在Java中，Lambda表达式是对象，他们必须依附于一类特别的对象类型——函数式接口（FunctionalInterface）。
	  


### 关于函数式接口
* 如果一个接口只有一个抽象方法，那么该接口就是一个函数式接口。
* 如果我们在某个接口上声明了FunctionalInterface注解，那么编译器就会按照函数式接口的定义来要求该接口。
* 如果某个接口只有一个抽象方法，但我们没有给该接口声明FunctionalInterface注解，
	那么编译器依旧会将该接口看作是函数式接口。
* 本质传递行为。

### lambda 概要
* “Lambda 表达式”(lambda expression)是一个匿名函数，Lambda表达式基于数学中的λ演算得名，直接对应于其中的lambda抽象(lambda abstraction)，是一个匿名函数，即没有函数名的函数。Lambda表达式可以表示闭包（注意和数学传统意义上的不同）。
* 传递行为，而不仅仅是值。
* 提升抽象层次。
* API 重用性更好。
* 更加灵活

#### lambda 示例
##### 接收两个int 类型的参数 返回两个参数相加的值
	（int a,int b）->{return a+b;}
##### 不接收参数 打印hello world
	()->{System.out.println("hello world")} 	
##### 不接收参数，返回一个值
	() -> 42 返回42
##### 不接收参数，返回一个值
	() ->{return 3.14}

#### lambda两种形式
##### 表达式
##### 语句
##### 区别于{}，表达式没有{}


##### Lambda 使用
	@FunctionalInterface
	interface MyInterface {
	    void test();
	    //有且只能有一个抽象方法
	    //    void test2();
	    //可以包含父类的方法
	    String toString();
	
	
	}
	public class Lambda {
	
	    public void myTest(MyInterface myInterface){
	        System.out.println("--------------------");
	        myInterface.test();
	        System.out.println("--------------------");
	    }
	
	    public static void main(String[] args ) {
	        Lambda lambda = new Lambda();
	        lambda.myTest(new MyInterface() {
	            @Override
	            public void test() {
	                System.out.println("functionalInterface1");
	            }
	        });
	
	        //-> 左边是参数  右边是实现体
	        lambda.myTest(()->{
	            System.out.println("functionalInterface2");
	        });
			
			//实现
	        MyInterface myInterface = ()->{System.out.println("functionalInterface3");};
	
	        lambda.myTest(myInterface);
	        System.out.println(myInterface.getClass());
	        System.out.println(myInterface.getClass().getSuperclass());
	        System.out.println(myInterface.getClass().getInterfaces()[0]);

	    }
	
	
	}

	输出：
		--------------------
		functionalInterface1
		--------------------
		--------------------
		functionalInterface2
		--------------------
		--------------------
		functionalInterface3
		--------------------
		class com.leyue.lambda.Lambda$$Lambda$2/1854731462
		class java.lang.Object
		interface com.leyue.lambda.MyInterface


#### consumer  消费
	@FunctionalInterface
	public interface Consumer<T> {

	    void accept(T t);

	  
	    default Consumer<T> andThen(Consumer<? super T> after) {
	        Objects.requireNonNull(after);
	        return (T t) -> { accept(t); after.accept(t); };
	    }
	}

##### forEach
	List<Integer> list = Arrays.asList(1,2,3,4,5);
    list.forEach(new Consumer<Integer>() {
        @Override
        public void accept(Integer integer) {
            System.out.println(integer);
        }
    });
    System.out.println("-------------------------");
    list.forEach((Integer i)->{System.out.println(i);});
    //方法引用
    list.forEach(System.out::println);

    Iterable forEach
    	default void forEach(Consumer<? super T> action) {
    	    Objects.requireNonNull(action);
    	    for (T t : this) {
    	        action.accept(t);
    	    }
    	}

##### 实现Consumer
	Consumer<Integer> consumer = (t)->{System.out.println(t);};
    consumer.accept(2);
    System.out.println("---------------");
    Consumer<Integer> consumer1 = new Consumer<Integer>() {
        @Override
        public void accept(Integer integer) {
            System.out.println("integer = "+integer);
        }
    };
    consumer1.accept(10);

##### andThen 返回一个Consumer 实现类
	//先执行当前的accept 再执行after的accept
	default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }

    int i = 2;
    Consumer<Integer> consumer = (t)->{
        t+=2;
        System.out.println("this i= "+t);
    };
    Consumer<Integer> consumer2 = (t)->{
        System.out.println("after: i= "+t);
    };
    consumer.andThen(consumer2).accept(i);
    
    输出：
    	this i= 4
		after i= 2


#### lambda使用示例
##### 字符串转换大写输出
	public void demo5() {
        //转换成大写输出
        List<String> list = Arrays.asList("hello", "world", "hello world");

        //方式一
        for (String data : list) {
            System.out.println(data.toUpperCase());
        }

        System.out.println("-----------");

        //方式二
        List<String> list1 = new ArrayList<>();
        list.forEach(item -> list1.add(item.toUpperCase()));
        list1.forEach(item -> System.out.println(item));

        System.out.println("-----------");

        //方式三
        list.stream().map(item-> item.toUpperCase()).forEach(System.out::println);

        System.out.println("-----------");

        //方式四
        list.stream().map(String::toUpperCase).forEach(System.out::println);

    }

### Function  有输入和输出
	@FunctionalInterface
	public interface Function<T, R> {

	    R apply(T t);

	   
	    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
	        Objects.requireNonNull(before);
	        return (V v) -> apply(before.apply(v));
	    }

	   
	    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
	        Objects.requireNonNull(after);
	        return (T t) -> after.apply(apply(t));
	    }

	   
	    static <T> Function<T, T> identity() {
	        return t -> t;
	    }
	}

#### 示例说明
	public class User {

	    public User show() {
	        System.out.println("---------------show()-------------");
	        return this;
	    }
	}	

	 @Test
    public void demo1() {
        Function<User,User> function1 =  User::show;
        Function<User,User> function2 =  user -> user.show();

        function1.apply(new User());
        function2.apply(new User());

    }
    输出：
    	---------------show()-------------
        ---------------show()-------------
---------------------------------------------------------------------------        
    public void demo2(){
        List<String> names = Arrays.asList("zhangsan","lisi","wangwu","zhaoliu");

        Collections.sort(names, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o2.compareTo(o1);
            }
        });

        System.out.println(names);
        System.out.println("------------------------");

        Collections.sort(names,(o1,o2)->{
            return o2.compareTo(o1);
        });

        System.out.println(names);
        System.out.println("------------------------");

        Collections.sort(names,(o1,o2)->o2.compareTo(o1));
        System.out.println(names);
        System.out.println("------------------------");

        Collections.sort(names,Collections.reverseOrder());
        System.out.println(names);

    }    
---------------------------------------------------------------------------
	 public void demo3() {
        System.out.println(compute(1, val -> val * 2));
        System.out.println(compute(2, val -> val + 2));
        System.out.println(compute(3, val -> val * val));

        System.out.println(convert(4, val -> String.valueOf(val + " hello")));

    }


    public int compute(int a, Function<Integer, Integer> function) {
        int res = function.apply(a);
        return res;
    }

    public String convert(int a, Function<Integer, String> function) {
        return function.apply(a);
    }

    输出：
    	2
		4
		9
		4 hello

---------------------------------------------------------------------------

##### 函数式接口
* 函数式接口的实现可以是 lambda expressions, method references, or constructor references.
* User::show 方式表示  User的show 方法必须返回值的是 Function接口的输出参数类型。
* User::show 方式表示 Function接口的输入参数在调用User类的show方法，相当于输入参数.show().
* Function<User,User> 的apply 输入参数类型要和Function 输入的泛型类型一致。

##### Function conpose andThen 使用
	default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }
----------------------------------------------------------------------
	default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }

* compose 返回一个Function。
* compose 先执行before的apply,得出结果当作调用者的输入参数。
* andThen 返回一个Function。
* andThren 先执行当前调用者的apply,得出结果当作after的输入参数。
#####示例
	 public int compose(int a, Function<Integer, Integer> function1, Function<Integer, Integer> function2) {
        return function1.compose(function2).apply(a);
    }
-----------------------------------------------------------------------------
    public int andThen(int a, Function<Integer, Integer> function1, Function<Integer, Integer> function2) {
        return function1.andThen(function2).apply(a);
    }

-----------------------------------------------------------------------------
   	public void demo4() {
        int result = compose(2, val -> val * 3, val -> val * val);
        System.out.println("compose= " + result);

        int result1 =  andThen(2, val -> val * 3, val -> val * val);
        System.out.println("andThen= "+result1);
    }
    输出结果：
    	compose= 12
		andThen= 36
-----------------------------------------------------------------------------

### BiFunction
#### 源码
	@FunctionalInterface
	public interface BiFunction<T, U, R> {

	    R apply(T t, U u);

	    default <V> BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after) {
	        Objects.requireNonNull(after);
	        return (T t, U u) -> after.apply(apply(t, u));
	    }
	}
* BiFunction 是Function 的扩展  接收两个参数 返回一个值。
* andThen 先执行BiFunction自己的apply 得到结果当作Function的apply的输入参数。

#### 加减乘除 使用
	public int coumpute(int a, int b, BiFunction<Integer, Integer, Integer> function) {
        return function.apply(a, b);
    }
-----------------------------------------------------------------------------
    public void demo1() {
        System.out.println(coumpute(1, 2, (val1, val2) -> val1 + val2));
        System.out.println(coumpute(1, 2, (val1, val2) -> val1 - val2));
        System.out.println(coumpute(1, 2, (val1, val2) -> val1 * val2));
        System.out.println(coumpute(1, 2, (val1, val2) -> val1 / val2));

    }
    输出结果：
    	3
		-1
		2
		0
-----------------------------------------------------------------------------
	public int andThren(int a, int b, BiFunction<Integer, Integer, Integer>biFunction, Function<Integer, Integer> function) {
        return biFunction.andThen(function).apply(a, b);
    }		
    public void demo2() {
        int result = andThren(2, 3, (val1, val2) -> val1 + val2, val -> val * val);
        System.out.println("result= "+result);
    }
    输出：
    	result= 25


#### 示例2
     public void demo3(){
        List<User> datas = new ArrayList<>();

        datas.add(new User("张三",34));
        datas.add(new User("李四",20));
        datas.add(new User("王五",40));
        datas.add(new User("李浩",26));

        List<User> result = filter1(30, datas);
        
        result.forEach(bean->System.out.println(bean));
        
        System.out.println("--------------------");
        //传递行为
        filter2(30, datas,(age,list)->list.stream().filter(user -> user.getAge()>age).collect(Collectors.toList()))
                .forEach(bean->System.out.println(bean));

    }
------------------------------------------------------------------------
    //语句
    private List<User> filter(int age,List<User> list) {
        BiFunction<Integer,List<User>  ,List<User>  > biFunction = (Integer ageFilter,List<User> data)->{
            return data.stream().filter(bean->bean.getAge()>age).collect(Collectors.toList());
        };
        return biFunction.apply(age,list);
    }
------------------------------------------------------------------------
    //表达式
    private List<User> filter1(int age,List<User> list) {
        BiFunction<Integer,List<User>  ,List<User>  > biFunction = (Integer ageFilter,List<User> data)->
             data.stream().filter(bean->bean.getAge()>age).collect(Collectors.toList());

        return biFunction.apply(age,list);
    }
------------------------------------------------------------------------
    //lambda 表达式传递行为
    private List<User> filter2(int age,List<User> list ,BiFunction<Integer,List<User>,List<User>> biFunction){
        return biFunction.apply(age,list);
    }




























