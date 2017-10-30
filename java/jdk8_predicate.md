## predicate 断言用于做判断
####源码
	public interface Predicate<T> {

    boolean test(T t);

   
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }

    
    default Predicate<T> negate() {
        return (t) -> !test(t);
    }

   
    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }

   
    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
   	}
	}

* 传入一个值 返回一个布尔值用于做判断

#### 实例
	 @Test
    public void test() {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        conditionFilter(list, item -> item % 2 == 0);
        System.out.println("-----------------");
        conditionFilter(list, item -> item % 3 == 0);
        System.out.println("-----------------");
        conditionFilter(list, item -> item > 5);
        System.out.println("-----------------");
        conditionFilter(list, item -> true);

    }

    @Test
    public void testand() {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        and(list, integer -> {
                    System.out.println("first");
                    return integer > 5;
                }, integer1 -> {
                    System.out.println("other");
                    return integer1 % 2 == 0;
                }
        );

    }

    @Test
    public void testnegate() {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        negate(list, integer -> integer > 5, integer1 -> integer1 % 2 == 0);
    }
    @Test
    public void testnegate3() {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        negate(list, integer -> {System.out.println("ff");return integer > 5;}, integer1 -> {System.out.println("dd"); return integer1 % 2 == 0;});
    }

    @Test
    public void testnegate2() {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        negate(list, integer -> {
            System.out.println("fff == "+integer);
            return integer > 5;
        }, integer1 -> {
            System.out.println("ddd == "+integer1);
            return integer1 % 2 == 0;});
    }


    public void conditionFilter(List<Integer> list, Predicate<Integer> predicate) {
        for (Integer integer : list) {
            if (predicate.test(integer)) {
                System.out.println(integer);
            }

        }
    }

    public void and(List<Integer> list, Predicate<Integer> predicate, Predicate<Integer> other) {
        for (Integer integer : list) {
            if (predicate.and(other).test(integer)) {
                System.out.println(integer);
            }

        }
    }

    public void negate(List<Integer> list, Predicate<Integer> predicate, Predicate<Integer> other) {
        for (Integer integer : list) {
            if (predicate.and(other).negate().test(integer)) {
                System.out.println(integer);
            }

        }
    }
    public void negate2(List<Integer> list, Predicate<Integer> predicate, Predicate<Integer> other) {
        for (Integer integer : list) {
//            if (predicate.and(other).negate().test(integer)) {
//                System.out.println(integer);
//            }
            predicate.and(other);

        }
    }

    @Test
    public void isequal() {
        boolean istrue = Predicate.isEqual(new Date()).test(new Date());
        System.out.print(istrue);
    }





