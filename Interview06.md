# 面试题目整理 06
<hr>   
  
一. 对象锁和类锁是否会互相影响？

* 对象锁：Java的所有对象都含有1个互斥锁，这个锁由JVM自动获取和释放。线程进入synchronized方法的时候获取该对象的锁，当然如果已经有线程获取了这个对象的锁，那么当前线程会等待；synchronized方法正常返回或者抛异常而终止，JVM会自动释放对象锁。这里也体现了用synchronized来加锁的1个好处，方法抛异常的时候，锁仍然可以由JVM来自动释放。

* 类锁：对象锁是用来控制实例方法之间的同步，类锁是用来控制静态方法（或静态变量互斥体）之间的同步。其实类锁只是一个概念上的东西，并不是真实存在的，它只是用来帮助我们理解锁定实例方法和静态方法的区别的。我们都知道，java类可能会有很多个对象，但是只有1个Class对象，也就是说类的不同实例之间共享该类的Class对象。Class对象其实也仅仅是1个java对象，只不过有点特殊而已。由于每个java对象都有1个互斥锁，而类的静态方法是需要Class对象。所以所谓的类锁，不过是Class对象的锁而已。获取类的Class对象有好几种，最简单的就是MyClass.class的方式。

* 类锁和对象锁不是同1个东西，一个是类的Class对象的锁，一个是类的实例的锁。也就是说：1个线程访问静态synchronized的时候，允许另一个线程访问对象的实例synchronized方法。反过来也是成立的，因为他们需要的锁是不同的。   

synchronized 加到 static 方法前面是给class 加锁，即类锁；而synchronized 加到非静态方法前面是给对象上锁。这两者的区别我用代码来演示下：  

* 对象锁和类锁是不同的锁，所以多个线程同时执行这2个不同锁的方法时，是异步的。  


在Task2 中定义三个方法 doLongTimeTaskA和doLongTimeTaskB是类锁，而doLongTimeTaskC是对象锁。  
  
	public class Task2 {

    public synchronized static void doLongTimeTaskA() {
        System.out.println("name = " + Thread.currentThread().getName() + ", begain");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("name = " + Thread.currentThread().getName() + ", end");
    }

    public synchronized static void doLongTimeTaskB() {
        System.out.println("name = " + Thread.currentThread().getName() + ", begain");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("name = " + Thread.currentThread().getName() + ", end");
    }

    public synchronized void doLongTimeTaskC() {

        System.out.println("name = " + Thread.currentThread().getName() + ", begain");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("name = " + Thread.currentThread().getName() + ", end");

    }  
  
三个线程的代码如下：  
  
	class ThreadA extends Thread{
 	   private Task2 mTask2;

		public ThreadA(Task2 tk){
        	mTask2 = tk;
    	}

    	public void run() {
        	mTask2.doLongTimeTaskA();
    	}
	}

	class ThreadB extends Thread{

    	private Task2 mTask2;

    	public ThreadB(Task2 tk){
    	    mTask2 = tk;
    	}

    	public void run() {
    	    mTask2.doLongTimeTaskB();
    	}
	}

	class ThreadC extends Thread{

    	private Task2 mTask2;

    	public ThreadC(Task2 tk){
    	    mTask2 = tk;
    	}

    	public void run() {
    	    mTask2.doLongTimeTaskC();
    	}
	}  
  
main函数中执行代码如下：  
  

        Task2 mTask2 = new Task2();
        ThreadA ta = new ThreadA(mTask2);
        ThreadB tb = new ThreadB(mTask2);
        ThreadC tc = new ThreadC(mTask2);

        ta.setName("A");
        tb.setName("B");
        tc.setName("C");

        ta.start();
        tb.start();
        tc.start();  
  
执行的结果如下：  
  
	name = A, begain, time = 1487311199783
	name = C, begain, time = 1487311199783
	name = C, end, time = 1487311200784
	name = A, end, time = 1487311200784
	name = B, begain, time = 1487311200784
	name = B, end, time = 1487311201784  
  
可以看出由于 doLongTimeTaskA和doLongTimeTaskB都是类锁，即同一个锁，所以 A和B是按顺序执行，即同步的。而C是对象锁，和A/B不是同一种锁，所以C和A、B是 异步执行的。（A、B、C代指上面的3中方法）。  
  
我们知道对象锁要想保持同步执行，那么锁住的必须是同一个对象。下面就修改下上面的来证明：  
  
Task2.java不变，修改ThreadA 和 ThreadB 如下：  
  
	class ThreadA extends Thread{

    	private Task2 mTask2;

    	public ThreadA(Task2 tk){
        	mTask2 = tk;
    	}

    	public void run() {
    	    mTask2.doLongTimeTaskC();
    	}
	}

	class ThreadB extends Thread{

    	private Task2 mTask2;

    	public ThreadB(Task2 tk){
    	    mTask2 = tk;
    	}

    	public void run() {
    	    mTask2.doLongTimeTaskC();
    	}
	}  
  
main方法如下：  
  
	Task2 mTaska = new Task2();
	Task2 mTaskb = new Task2();
	ThreadA ta = new ThreadA(mTaska );
	ThreadB tb = new ThreadB(mTaskb );

	ta.setName("A");
	tb.setName("B");

	ta.start();
	tb.start();  
  
结果如下：  
  
	name = A, begain, time = 1487311905775
	name = B, begain, time = 1487311905775
	name = B, end, time = 1487311906775
	name = A, end, time = 1487311906775  
  
从结果看来，对象锁锁的对象不一样，分别是mTaska ， mTaskb，所以线程A和线程B调用 doLongTimeTaskC 是异步执行的。

但是，类锁可以对类的所有对象的实例起作用。只需修改ThradA 
和 ThreadB，main 方法不做改变，修改如下：  
  
	class ThreadA extends Thread{

    	private Task2 mTask2;

    	public ThreadA(Task2 tk){
    	    mTask2 = tk;
    	}

    	public void run() {
    	    //mTask2.doLongTimeTaskC();
    	    mTask2.doLongTimeTaskA();
    	}
	}

	class ThreadB extends Thread{

    	private Task2 mTask2;

    	public ThreadB(Task2 tk){
    	    mTask2 = tk;
    	}

    	public void run() {
    	   //mTask2.doLongTimeTaskC();
    	   mTask2.doLongTimeTaskA();
    	}
	}  
  
结果如下：  
  
	name = A, begain, time = 1487312239674
	name = A, end, time = 1487312240674
	name = B, begain, time = 1487312240674
	name = B, end, time = 1487312241674  
  
可以看出 在线程A执行完doLongTimeTaskA方法后，线程B才会获得该类锁接着去执行doLongTimeTaskA。也就是说，类锁对所有的该类对象都能起作用。

总结： 
1. 如果多线程同时访问同一类的 类锁（synchronized 修饰的静态方法）以及对象锁（synchronized 修饰的非静态方法）这两个方法执行是异步的，原因：类锁和对象锁是2中不同的锁。 
2. 类锁对该类的所有对象都能起作用，而对象锁不能。