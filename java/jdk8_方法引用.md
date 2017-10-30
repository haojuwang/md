##方法引用
#### 方法引用可看作一个“函数指针” function pointer

### 方法引用分为4类
####1，类名::静态方法名

#####示例1：
	public void test1(){
        List<String> list = Arrays.asList("a", "b", "c");
        list.stream().forEach(System.out::println);
    }

####2，引用名（对象名）::实例方法名
	 public void test2(){
        List<User> datas = new ArrayList<>();
        datas.add(new User("zhangsan",34));
        datas.add(new User("lisi",20));
        datas.add(new User("wanglu",40));
        datas.add(new User("xiaoming",26));
        UserCompare comapre = new UserCompare();

        datas.sort((user1,user2)->comapre.compareByAge(user1,user2));
        datas.sort(comapre::compareByAge);
        datas.forEach(System.out::println);

    }

#### 3，类名::实例方法名
	  @Test
    //对象名::实例方法名
    public void test3() {
        List<User> datas = new ArrayList<>();
        datas.add(new User("zhangsan", 34));
        datas.add(new User("lisi", 20));
        datas.add(new User("wanglu", 40));
        datas.add(new User("xiaoming", 26));

        datas.sort(User::compareByAge);
        List<String> result = datas.stream().flatMap(user -> Stream.of(user.getUserName())).collect(Collectors.toList());
        System.out.println(result);
    }
* lamada 表达式第一个参数 调用实例方法，除了第一个参数其他参数当形参传递。

#### 4，构造方法引用： 类名::new










