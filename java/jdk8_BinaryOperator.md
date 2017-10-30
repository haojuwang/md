##BinaryOperator 继承BiFunction 比较器
####源码
	public interface BinaryOperator<T> extends BiFunction<T,T,T> {
   
    public static <T> BinaryOperator<T> minBy(Comparator<? super T> comparator) {
        Objects.requireNonNull(comparator);
        return (a, b) -> comparator.compare(a, b) <= 0 ? a : b;
    }

    
    public static <T> BinaryOperator<T> maxBy(Comparator<? super T> comparator) {
        Objects.requireNonNull(comparator);
        return (a, b) -> comparator.compare(a, b) >= 0 ? a : b;
    }
}

####示例
	 @Test
    public void test1() {
        Integer result1 = method1( 20, 10, (a, b) -> a * b);
        System.out.println(result1);
        Integer result2 = method1( 20, 10, (a, b) -> a - b);
        System.out.println(result2);

    }

    @Test
    public void test2() {
        String result = minBy("hello lihao","world",(a,b)->a.length()-b.length());
        System.out.println(result);

        String result1 = minBy("hello lihao","world",(a,b)->a.charAt(0)-b.charAt(0));
        System.out.println(result1);
    }


    public Integer method1(Integer a, Integer b, BinaryOperator<Integer> binaryOperator) {
        return binaryOperator.apply(a, b);
    }

    public String minBy(String a,String b,Comparator<String> comparable) {

        return BinaryOperator.minBy(comparable).apply(a,b);
    }