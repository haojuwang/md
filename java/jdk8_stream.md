jdk8_stream.md
## 流由3部分构成
####1，源
####2，零个或多个中间操作
####3，终止操作

## 流操作的分类
####1，惰性求值
####2，及早求值

## 流
* Collection提供了新的stream()方法。
* 流不存储值，通过管道的方式获取值。
* 本质是函数式的，对流的操作会生成一个结果，不过并不会修改底层的数据源，
	集合可以作为流的底层数据源。
* 延迟查找，很多流操作（过滤，映射，排序等）都可以延迟实现。	


## 创建流
	Stream stream1 = Stream.of("a","b","c");
    String[] myArray = new String[]{"a", "b", "c"};
    Stream<String> stream2 = Stream.of(myArray);
    Stream<String> stream3 = Arrays.stream(myArray);
    List<String> list = Arrays.asList(myArray);
    Stream<String> stream4 = list.stream();

## 案例
#### 求和
	 public void test2() {
        int sum = Stream.of(1, 2, 3, 4, 5, 6).map(i -> i * 2).reduce(0, Integer::sum);
        System.out.println(sum);
        int sum1 = Stream.of(1, 2, 3, 4, 5, 6).map(i -> i * 2).collect(Collectors.summingInt(a->a));
        System.out.println(sum1);

    }    

#### 流转换为集合
	 @Test
    public void test3() {
        Stream<String> stream = Stream.of("hello", "world", "你好");
        //实现方法传递行为
        String[] strArr = stream.toArray(length -> new String[length]);
        Arrays.asList(strArr).stream().forEach(System.out::println);

        //构造方法
        Stream<String> stream1 = Stream.of("hello", "world", "你好");
        String[] strArr2 = stream1.toArray(String[]::new);
        Arrays.asList(strArr2).stream().forEach(System.out::println);

        Stream<String> stream2 = Stream.of("hello", "world", "你好");
        ArrayList<String> list = stream2.collect(ArrayList::new,ArrayList::add,ArrayList::addAll);
        list.stream().forEach(System.out::println);
    }




