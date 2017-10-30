##Optional 避免空指针
#### 源码
	
public final class Optional<T> {
   
    private static final Optional<?> EMPTY = new Optional<>();

   
    private final T value;

   
    private Optional() {
        this.value = null;
    }

   
    public static<T> Optional<T> empty() {
        @SuppressWarnings("unchecked")
        Optional<T> t = (Optional<T>) EMPTY;
        return t;
    }

    
    private Optional(T value) {
        this.value = Objects.requireNonNull(value);
    }

    
    public static <T> Optional<T> of(T value) {
        return new Optional<>(value);
    }

   
    public static <T> Optional<T> ofNullable(T value) {
        return value == null ? empty() : of(value);
    }

  
    public T get() {
        if (value == null) {
            throw new NoSuchElementException("No value present");
        }
        return value;
    }

   
    public boolean isPresent() {
        return value != null;
    }

   
    public void ifPresent(Consumer<? super T> consumer) {
        if (value != null)
            consumer.accept(value);
    }

   
    public Optional<T> filter(Predicate<? super T> predicate) {
        Objects.requireNonNull(predicate);
        if (!isPresent())
            return this;
        else
            return predicate.test(value) ? this : empty();
    }

    
    public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Optional.ofNullable(mapper.apply(value));
        }
    }


    public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Objects.requireNonNull(mapper.apply(value));
        }
    }

    public T orElse(T other) {
        return value != null ? value : other;
    }

   
    public T orElseGet(Supplier<? extends T> other) {
        return value != null ? value : other.get();
    }

  
    public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X {
        if (value != null) {
            return value;
        } else {
            throw exceptionSupplier.get();
        }
    }

  
    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }

        if (!(obj instanceof Optional)) {
            return false;
        }

        Optional<?> other = (Optional<?>) obj;
        return Objects.equals(value, other.value);
    }

    
    @Override
    public int hashCode() {
        return Objects.hashCode(value);
    }

    
    @Override
    public String toString() {
        return value != null
            ? String.format("Optional[%s]", value)
            : "Optional.empty";
    }
}

#### 示例
	 @Test
    public void test1() {

        Optional<String> optional = Optional.of("hello");
        optional.ifPresent((a) -> System.out.println(a));

        System.out.println("----------");
        Optional<String> optional1 = Optional.empty();
        System.out.println(optional1.orElse("nihao"));

        System.out.println("----------");
        Optional<String> optional2 = Optional.ofNullable(null);
        System.out.println(optional2.orElseGet(() -> "world"));
    }


    @Test
    public void test2() {
        User user =new User();
        Addreess addres1 = new Addreess("北京");
        Addreess addres2 = new Addreess("上海");

        List<Addreess> list = Arrays.asList(addres1, addres2);

        user.setAddress(list);

        Optional<User> optional = Optional.ofNullable(user);

        List<Addreess> data = optional.map(user1 -> user1.getAddress()).orElse(Collections.emptyList());
        System.out.println(data);

    }

* Optional 不能作为参数和成员变量。
* 避免空指针。 














