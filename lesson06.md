# Java基础06  
<hr>     

## 多线程   
* Thread.currentThread();返回对当前正在执行的线程对象的引用
* 创建新执行线程有两种方法。
	* 一种方法是将类声明为 Thread 的子类。该子类应重写 Thread 类的 run 方法。
	* 创建线程的另一种方法是声明实现 Runnable 接口的类。该类然后实现 run 方法。然后可以分配该类的实例，在创建 Thread 时作为一个参数来传递并启动。
    
---


     class PrimeRun implements Runnable {
         long minPrime;
         PrimeRun(long minPrime) {
             this.minPrime = minPrime;
         }
 
         public void run() {
             // compute primes larger than minPrime
              . . .
         }
     }
然后，下列代码会创建并启动一个线程： 


     PrimeRun p = new PrimeRun(143);
     new Thread(p).start();  

---
  
     class PrimeThread extends Thread {
         long minPrime;
         PrimeThread(long minPrime) {
             this.minPrime = minPrime;
         }
 
         public void run() {
             // compute primes larger than minPrime
              . . .
         }
     }
然后，下列代码会创建并启动一个线程： 


     PrimeThread p = new PrimeThread(143);
     p.start();
  
---
* 安全问题
	* 同步代码块：对多条操作共享数据的语句，进行同步代码块操作
	* 同步函数：public synchronized void add(int n) ;同步函数线程锁是this
	
* 单例设计模式

		//饿汉式  
		class Single
		{
			private static final Single s = new Singls();
			private Single()
			{
			}

			public static Single getInstance()
			{
				return s;
			}

		}


		//懒汉式
		class Single
		{
			private static Single s = null;
			private Single(){}
			
			public static Single getInstance()
			{
				if(s==null)//提高效率。避免多次判断
				{
					synchronized(Single.class)
					{
						if(s==null)
							s = new Single();
					}
				}
				return s;
			}
		}