# 面试总结03 
<hr>   
  
## mysql隔离级别与悲观锁、乐观锁
1. 什么是悲观锁，乐观锁
悲观锁大多数情况下依靠数据库的锁机制实现，以保证操作最大程度的独占性。但随之而来的就是数据库性能的大量开销，特别是对长事务而言，这样的开销往往无法承受。而乐观锁机制在一定程度上解决了这个问题。乐观锁，大多是基于数据版本（ Version ）记录机制实现。何谓数据版本？即为数据增加一个版本标识，在基于数据库表的版本解决方案中，一般是通过为数据库表增加一个 “version” 字段来实现。读取出数据时，将此版本号一同读出，之后更新时，对此版本号加一。此时，将提交数据的版本数据与数据库表对应记录的当前版本信息进行比对，如果提交的数据版本号大于数据库表当前版本号，则予以更新，否则认为是过期数据。

* 悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。[1]
* 乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。[1]乐观锁不能解决脏读的问题。
* 使用自增长的整数表示数据版本号。更新时检查版本号是否一致，比如数据库中数据版本为6，更新提交时version=6+1,使用该version值(=7)与数据库version+1(=7)作比较，如果相等，则可以更新，如果不等则有可能其他程序已更新该记录，所以返回错误。

----
1. 悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

2. 乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。

3. 两种锁各有优缺点，不可认为一种好于另一种，像乐观锁适用于写比较少的情况下，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果经常产生冲突，上层应用会不断的进行retry，这样反倒是降低了性能，所以这种情况下用悲观锁就比较合适。
  
---

## java线程唤醒与阻塞
1. sleep() 方法：sleep() 允许 指定以毫秒为单位的一段时间作为参数，它使得线程在指定的时间
内进入阻塞状态，不能得到CPU 时间，指定的时间一过，线程重新进入可执行状态。
	* 典型地，sleep() 被用在等待某个资源就绪的情形：测试发现条件不满足后，让线程阻塞一段时间后
重新测试，直到条件满足为止。
2. suspend() 和 resume() 方法：两个方法配套使用，suspend()使得线程进入阻塞状态，并且不会
自动恢复，必须其对应的resume() 被调用，才能使得线程重新进入可执行状态。典型地，suspend() 和 
resume() 被用在等待另一个线程产生的结果的情形：测试发现结果还没有产生后，让线程阻塞，另一个
线程产生了结果后，调用 resume() 使其恢复。
3. yield() 方法：yield() 使得线程放弃当前分得的 CPU 时间，但是不使线程阻塞，即线程仍处于
可执行状态，随时可能再次分得 CPU 时间。调用 yield() 的效果等价于调度程序认为该线程已执行了足
够的时间从而转到另一个线程。
4. wait() 和 notify() 方法：两个方法配套使用，wait() 使得线程进入阻塞状态，它有两种形式
，一种允许指定以毫秒为单位的一段时间作为参数，另一种没有参数，前者当对应的 notify() 被调用或
者超出指定时间时线程重新进入可执行状态，后者则必须对应的 notify() 被调用。
	* 初看起来它们与 suspend() 和 resume() 方法对没有什么分别，但是事实上它们是截然不同的。区
别的核心在于，前面叙述的所有方法，阻塞时都不会释放占用的锁（如果占用了的话），而这一对方法则
相反。

　　上述的核心区别导致了一系列的细节上的区别。

* 首先，前面叙述的所有方法都隶属于 Thread 类，但是这一对却直接隶属于 Object 类，也就是说，
所有对象都拥有这一对方法。初看起来这十分不可思议，但是实际上却是很自然的，因为这一对方法阻塞
时要释放占用的锁，而锁是任何对象都具有的，调用任意对象的 wait() 方法导致线程阻塞，并且该对象
上的锁被释放。而调用 任意对象的notify()方法则导致因调用该对象的 wait() 方法而阻塞的线程中随
机选择的一个解除阻塞（但要等到获得锁后才真正可执行）。

* 其次，前面叙述的所有方法都可在任何位置调用，但是这一对方法却必须在 synchronized 方法或块
中调用，理由也很简单，只有在synchronized 方法或块中当前线程才占有锁，才有锁可以释放。同样的
道理，调用这一对方法的对象上的锁必须为当前线程所拥有，这样才有锁可以释放。因此，这一对方法调
用必须放置在这样的 synchronized 方法或块中，该方法或块的上锁对象就是调用这一对方法的对象。若
不满足这一条件，则程序虽然仍能编译，但在运行时会出现 IllegalMonitorStateException 异常。

* wait() 和 notify() 方法的上述特性决定了它们经常和synchronized 方法或块一起使用，将它们和
操作系统的进程间通信机制作一个比较就会发现它们的相似性：synchronized方法或块提供了类似于操作
系统原语的功能，它们的执行不会受到多线程机制的干扰，而这一对方法则相当于 block 和wakeup 原语
（这一对方法均声明为 synchronized）。它们的结合使得我们可以实现操作系统上一系列精妙的进程间
通信的算法（如信号量算法），并用于解决各种复杂的线程间通信问题。

* 关于 wait() 和 notify() 方法最后再说明两点：
* 第一：调用 notify() 方法导致解除阻塞的线程是从因调用该对象的 wait() 方法而阻塞的线程中随
机选取的，我们无法预料哪一个线程将会被选择，所以编程时要特别小心，避免因这种不确定性而产生问
题。

* 第二：除了 notify()，还有一个方法 notifyAll() 也可起到类似作用，唯一的区别在于，调用 
notifyAll() 方法将把因调用该对象的 wait() 方法而阻塞的所有线程一次性全部解除阻塞。当然，只有
获得锁的那一个线程才能进入可执行状态。
  
---  
  
## 单例模式
###实现方式：懒汉单例类和饿汉单例类
* 懒汉式单例类  
	对于懒汉模式，我们可以这样理解：该单例类非常懒，只有在自身需要的时候才会行动，从来不知道及早做好准备。它在需要对象的时候，才判断是否已有对象，如果没有就立即创建一个对象，然后返回，如果已有对象就不再创建，立即返回。
	懒汉模式只在外部对象第一次请求实例的时候才去创建。
* 饿汉式单例  
	对于饿汉模式，我们可以这样理解：该单例类非常饿，迫切需要吃东西，所以它在类加载的时候就立即创建对象。

* 懒汉模式和饿汉模式的优缺点：  
	* 懒汉模式，它的特点是运行时获得对象的速度比较慢，但加载类的时候比较快。它在整个应用的生命周期只有一部分时间在占用资源。  
	* 饿汉模式，它的特点是加载类的时候比较慢，但运行时获得对象的速度比较快。它从加载到应用结束会一直占用资源。  
	* 这两种模式对于初始化较快，占用资源少的轻量级对象来说，没有多大的性能差异，选择懒汉式还是饿汉式都没有问题。但是对于初始化慢，占用资源多的重量级对象来说，就会有比较明显的差别了。所以，对重量级对象应用饿汉模式，类加载时速度慢，但运行时速度快；懒汉模式则与之相反，类加载时速度快，但运行时第一次获得对象的速度慢。  
	* 从用户体验的角度来说，我们应该首选饿汉模式。我们愿意等待某个程序花较长的时间初始化，却不喜欢在程序运行时等待太久，给人一种反应迟钝的感觉，所以对于有重量级对象参与的单例模式，我们推荐使用饿汉模式。  
	
懒汉式	  

	public class Singleton{  
    	private static Singleton instance = null;  
    	private Singleton(){}  
    	public static Singleton newInstance(){  
    	    if(null == instance){  
    	        instance = new Singleton();  
    	    }  
    	    return instance;  
    	}  
	} 

饿汉式  

	public class Singleton{  
	    private static Singleton instance = new Singleton();  
	    private Singleton(){}  
	    public static Singleton newInstance(){  
	        return instance;  
	    }  
	}     
   
静态内部类   
   
	public class Singleton{  
	    private static class SingletonHolder{  
	        public static Singleton instance = new Singleton();  
	    }  
	    private Singleton(){}  
	    public static Singleton newInstance(){  
	        return SingletonHolder.instance;  
	    }  
	}  
   
  
## 使用软引用构建缓存  
  
一、为什么要使用软引用  

关于软引用的介绍中，已经提到了软引用的特性。使用SoftReference引用的对象会有很长的生命周期，只有当系统的内存不足的时候，才会去释放这些软引用对象。所以可以使用软引用来缓存一些比较昂贵的资源，比如获取的网络图片数据。  
  
当应用从网络中获取网络图片数据时，用户完全有可能做一些重复性的操作去查看相同的图片信息。对于这样的问题，通常会有两种解决方法: 一种是把过去查看过的图片信息保存在内存中，每一个存储了图片信息的 Java 对象的生命周期都贯穿整个应用程序生命周期，另一种是当用户开始查看其他图片信息的时候，把存储了当前的图片信息的 Java 对象结束引用，使得垃圾收集线程可以回收其所占用的内存空间，当用户再次需要浏览该图片信息的时候，重新获取图片信息。  
  
很显然，第一种实现方法将造成大量的内存浪费，而第二种实现的缺陷在于即使垃圾收集线程还没有进行垃圾收集，包含图片信息的对象仍然完好地保存在内存中，应用程序也要重新构建一个对象。  
  
像访问磁盘文件、访问网络资源、查询数据库等操作都是影响应用程序执行性能的重要因素，如果能重新获取那些尚未被回收的 Java 对象的引用，必将减少不必要的访问，大大提高程序的运行速度。   
  
二、具体描述：  
1．强引用  
以前我们使用的大部分引用实际上都是强引用，这是使用最普遍的引用。如果一个对象具有强引用，那就类似于必不可少的生活用品，垃圾回收器绝不会回收它。当内存空 间不足，Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足问题。 如：  

	String str = "abc";
	List<String> list = new Arraylist<String>();
	list.add(str);  
  
在list集合里的数据不会释放，即使内存不足也不会  

2、软引用（SoftReference）   
如果一个对象只具有软引用，那就类似于可有可物的生活用品。如果内存空间足够，垃圾回收器就不会回收它，如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。   
软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收，JAVA虚拟机就会把这个软引用加入到与之关联的引用队列中。 如：  

	public class Test {  

    	public static void main(String[] args){  
        	System.out.println("开始");            
        	A a = new A();            
        	SoftReference<A> sr = new SoftReference<A>(a);  
        	a = null;  
        	if(sr!=null){  
        	    a = sr.get();  
        	}  
        	else{  
        	    a = new A();  
        	    sr = new SoftReference<A>(a);  
        	}            
        	System.out.println("结束");     
    	}       

	}  

	class A{  
    	int[] a ;  
    	public A(){  
    	    a = new int[100000000];  
    	}  
	}  
  
当内存足够大时可以把数组存入软引用，取数据时就可从内存里取数据，提高运行效率  

3．弱引用（WeakReference）  
如果一个对象只具有弱引用，那就类似于可有可物的生活用品。弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它 所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程， 因此不一定会很快发现那些只具有弱引用的对象。   
弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java虚拟机就会把这个弱引用加入到与之关联的引用队列中。 如：  

	Object c = new Car(); //只要c还指向car object, car object就不会被回收
	WeakReference<Car> weakCar = new WeakReference(Car)(car);
  
当要获得weak reference引用的object时, 首先需要判断它是否已经被回收:   

	weakCar.get();

如果此方法为空, 那么说明weakCar指向的对象已经被回收了.  

下面来看一个例子:  

	public class Car {
  	private double price;
  	private String colour;

  	public Car(double price, String colour){
    	this.price = price;
    	this.colour = colour;
  	}

  	public double getPrice() {
    	return price;
  	}
  	public void setPrice(double price) {
    	this.price = price;
  	}
  	public String getColour() {
    	return colour;
  	}
  	public void setColour(String colour) {
    	this.colour = colour;
  	}

  	public String toString(){
    	return colour +"car costs $"+price;
  	}

	}

	public class TestWeakReference {

	public static void main(String[] args) {

    Car car = new Car(22000,"silver");
    WeakReference<Car> weakCar = new WeakReference<Car>(car);

    int i=0;

    while(true){
      if(weakCar.get()!=null){
        i++;
        System.out.println("Object is alive for "+i+" loops - "+weakCar);
      }else{
        System.out.println("Object has been collected.");
        break;
      }
    }
	}

	}
   

在上例中, 程序运行一段时间后, 程序打印出”Object has been collected.” 说明, weak reference指向的对象的被回收了.  

如果要想打出的是  

	Object is alive for “+i+” loops - “+weakCar

那么只要在这句话前面加上   

	System.out.println(“car==== “+car); 

因为在此强引用了car对象

4．虚引用（PhantomReference）  
 
“虚引用”顾名思义，就是形同虚设，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收。   

虚引用主要用来跟踪对象被垃圾回收的活动。虚引用与软引用和弱引用的一个区别在于：虚引用必须和引用队列（ReferenceQueue）联合使用。当垃 圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是 否已经加入了虚引用，来了解   

被引用的对象是否将要被垃圾回收。程序如果发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采取必要的行动。 
特别注意，在世纪程序设计中一般很少使用弱引用与虚引用，使用软用的情况较多，这是因为软引用可以加速JVM对垃圾内存的回收速度，可以维护系统的运行安全，防止内存溢出（OutOfMemory）等问题的产生。  

总结：  
强引用：   
String str = “abc”;   
list.add(str);   
软引用：   
如果弱引用对象回收完之后，内存还是报警，继续回收软引用对象   
弱引用：   
如果虚引用对象回收完之后，内存还是报警，继续回收弱引用对象   
虚引用：   
虚拟机的内存不够使用，开始报警，这时候垃圾回收机制开始执行System.gc(); String s = “abc”;如果没有对象回收了， 就回收没虚引用的对象    
  
二、如何使用软引用  
  
SoftReference 的特点是它的一个实例保存着一个 Java 对象的软引用，该软引用的存在不妨碍垃圾收集器线程对该 Java 对象的回收。也就是说，一旦SoftReference 保存着一个 Java 对象的软引用之后，在垃圾收集器线程对这个 Java 对象回收之前， SoftReference 类所提供的 get() 方法都会返回 这个Java 对象的强引用。另外，一旦垃圾线程回收该 Java 对象之后， get() 方法将返回 null 。  
  
软引用的使用方法如下面的Java代码所示 ：  
  
	MyObject aRef = new MyObject();//创建一个对象
	SoftReference aSoftRef = new SoftReference(aRef);//创建对象的软引用  
  
上面的代码执行后，对于MyObject 对象，有两个引用路径，一个是来自 aSoftRef对象的软引用，一个来自变量 aRef 的强引用，所以 MyObject对象是强可及对象。紧跟着，可以使用下面的java的代码结束 aReference 对 MyObject 实例的强引用 ：  
  
	aRef = null ;//断开对象的强引用  
  
此后， MyObject 对象成为了软可及对象。如果垃圾收集线程进行内存垃圾收集，并不会因为有一个 SoftReference 对该对象的引用而始终保留该对象。 Java 虚拟机的垃圾收集线程对软可及对象和其他一般 Java 对象进行了区别对待 ，软可及对象的清理是由垃圾收集线程根据其特定算法按照内存需求决定的。也就是说，垃圾收集线程会在虚拟机抛出 OutOfMemoryError 之前回收软可及对象，而且虚拟机会尽可能优先回收长时间闲置不用的软可及对象，对那些刚刚构建的或刚刚使用过的“新”软可及对象会被虚拟机尽可能保留。如果想获取软引用中包含的对象，可以使用下面的Java代码：  
  
	MyObject anotherRef =(MyObject) aSoftRef .get();//通过软引用获取对象  
  
在回收这些对象之前，可以通过上面的代码重新获得对该实例的强引用。而回收之后，当调用软引用的get() 方法时，返回的是 null  
  
三、如何使用 ReferenceQueue

作为一个 Java 对象， SoftReference 对象除了具有保存软引用的特殊性之外，也具有 Java 对象的一般性。所以，当软可及对象被回收之后，虽然这个 SoftReference 对象的 get() 方法返回 null, 但这个 SoftReference 对象已经不再具有存在的价值，需要一个适当的清除机制，避免大量 SoftReference 对象带来的内存泄漏。在 java.lang.ref 包里还提供了 ReferenceQueue 。如果在创建 SoftReference 对象的时候，使用了带有一个 ReferenceQueue 对象作为参数的构造方法，如下面的Java代码 :  
  
	ReferenceQueue queue = new ReferenceQueue();//创建引用队列
	SoftReference  ref = new SoftReference( aMyObject， queue );// 把引用加入到引用队列  
  
当这个 SoftReference 所软引用的 aMyOhject 被垃圾收集器回收的同时，ref 所强引用的 SoftReference 对象被列入 ReferenceQueue 。也就是说， ReferenceQueue 中保存的对象是 Reference 对象，而且是已经失去了它所软引用的对象的 Reference 对象。另外从 ReferenceQueue 这个名字也可以看出，它是一个队列，当调用它的 poll() 方法的时候，如果这个队列中不是空队列，那么将返回队列前面的那个 Reference 对象。  
  
在任何时候，都可以调用 ReferenceQueue 的 poll() 方法来检查是否有它所关心的非强可及对象被回收。如果队列为空，将返回一个 null, 否则该方法返回队列中最前面一个 Reference 对象。利用这个方法，可以检查哪个 SoftReference 所软引用的对象已经被回收。可以把这些失去所软引用的对象的 SoftReference 对象清除掉，如下面的Java代码所示。:  
  
	SoftReference ref = null ;
	while ((ref = (EmployeeRef) q .poll()) != null ) {
	// 清除 ref
	}
  
