## supplier 参数为空得到一个对象
public interface Supplier<T> {

    T get();
}
#### 示例
	 public void test() {
        Supplier<User> supplier = () -> new User();
        System.out.println(supplier.get().getUserName());
        Supplier<User> supplier2 =  User::new;
        System.out.println(supplier2.get().getUserName());
    }



















