# ThreadLocal

## ThreadLocal是什么
	ThreadLocal为解决多线程程序的并发问题提供一种新的思路。
	使用这个工具类可以很简洁地编写出有优美的多线程程序
	
## ThreadLocal 有什么用
* 保护共享资源不受污染。
* 当使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都一个独立改变自己的副本，而不会影响其它线程所对应的副本。
* 从线程的角度看，目标变量就像是线程的本地变量，这也是类名中“Local”所要表达的意思。
* 数据库连接池 

## ThreadLocal如何用
* void set(Object value)设置当前线程的线程局部变量的值。
* public Object get()该方法返回当前线程所对应的线程局部变量。
* public void remove()将当前线程局部变量的值删除，目的是为了减少内存的占用，该方法是JDK 5.0新增的方法。
* protected Object initialValue()返回该线程局部变量的初始值，该方法是一个protected的方法，显然是为了让子类覆盖而设计的。这个方法是一个延迟调用方法，在线程第1次调用get()或set(Object)时才执行，并且仅执行1次。ThreadLocal中的缺省实现直接返回一个null。

###### 代码示例
	//不使用 ThreadLocal
	 class TestNum{
		private int num =0;
		public int getNextNum() {
			return num++;
		}	
	}
------------------------------------------------------------	
	class TestCLient extends Thread {
		private TestNum testNum;

		public TestCLient(TestNum testNum) {
			this.testNum = testNum;
		}

		@Override
		public void run() {
			super.run();
			for(int i=0;i<3;i++) {
				System.out.println("thread[" + Thread.currentThread().getName()
						+ "]  -------> i=" + testNum.getNextNum());
			}
			
		}

	}
------------------------------------------------------------
	TestNum testNum = new TestNum();
	TestCLient t1 = new TestCLient(testNum);
	TestCLient t2 = new TestCLient(testNum);
	TestCLient t3 = new TestCLient(testNum);
	t1.start();
	t2.start();
	t3.start();
------------------------------------------------------------
	输出结果：
		thread[Thread-1]  -------> i=1
		thread[Thread-0]  -------> i=1
		thread[Thread-0]  -------> i=3
		thread[Thread-2]  -------> i=0
		thread[Thread-2]  -------> i=5
		thread[Thread-2]  -------> i=6
		thread[Thread-0]  -------> i=4
		thread[Thread-1]  -------> i=2
		thread[Thread-1]  -------> i=7
	每个线程的i都会被污染	
----------------------------------------------------------------
	//使用 ThreadLocal
	 class TestNum2{
		private   ThreadLocal<Integer> tl = new ThreadLocal<Integer>();
		public int getNextNum() {
			Integer a = tl.get();
			if(a == null) {
				System.out.println("a == "+ Thread.currentThread().getName());
				a =0;
				tl.set(a);
			}
			tl.set(++a);
				
			return tl.get();
		}	
	} 

	输出结果：
	a == Thread-0
	thread[Thread-0]  -------> i=1
	thread[Thread-0]  -------> i=2
	thread[Thread-0]  -------> i=3
	a == Thread-2
	thread[Thread-2]  -------> i=1
	thread[Thread-2]  -------> i=2
	thread[Thread-2]  -------> i=3
	a == Thread-1
	thread[Thread-1]  -------> i=1
	thread[Thread-1]  -------> i=2
	thread[Thread-1]  -------> i=3

	每个线程的i都会是局部变量其他线程不会污染	
 














