# 面试总结01 
<hr>   
  
## Log4j
* Log4j是Apache的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件，甚至是套接口服务器、NT的事件记录器、UNIX Syslog守护进程等；  
  
![](https://i.imgur.com/pGuC6UY.png)  
![](https://i.imgur.com/pKZOYjt.png)  
  
## DIV  
* DIV是层叠样式表中的定位技术   
  
## 创建线程的两种方式？
* 创建线程的第一种方式：继承Thread类
	* 步骤：
	* （1）定义类继承Thread
	* （2）覆写Thread类中的run方法
	* （3）调用线程的start方法，该方法两个作用：启动线程，调用run方法
* 创建线程的第二种方式：实现Runnable接口
	* 步骤：1.定义类实现Runnable接口；
	* 2.覆盖Runnable接口中的run方法，将线程要运行的代码存放在该run方法中
	* 3.通过Thread类建立线程对象；
	* 4.将Runnable接口的子类对象作为实际参数传递给Thread类的构造函数。new Thread(t)；
	* 5.调用Thread类的start方法开启线程并调用Runnable接口子类的run方法 
## 实现方式和继承方式有什么区别呢？（面试题经常考）
* 实现方式好处：避免了单继承的局限性，如果继承了一个类就不能再继承其他类了，在定义线程时，建议使用实现方式。
* 两种方式区别：
	* 继承Thread线程代码存放Thread子类run方法中
	* 实现Runnable，线程代码存放在接口的子类的run方法
 
## 使用多线程机制会提高程序的运行效率吗？
* 不会，因为多个线程都获取cpu的执行权，cpu执行到谁，谁就运行，明确一点，在某个时刻，只能有一个程序在运行。（多核除外）cpu在做着快速的切换，以达到看上去是同时运行的效果，我们可以形象把多线程的运行行为在互相抢夺cpu的执行权，这就是多线程的一个特性：随机性。即谁抢到谁执行，至于执行多长，cpu说的算。但是这样就好像是一个厨师在一个桌子上做馒头变成不停的换到别的桌子上做馒头，做的效率不会提高，而且性能还会更低。
## 为什么会有多线程下载？
* 多线程运行效率并不会快，但是多线程会抢带宽，比如一个线程10k的下载，我们用20个线程就变成了200K，就是20个线程一起下载，这样就快啦。  
  
## 异步任务加载网络数据——AsyncTask使用  

* Android从1.5开始引入了AsyncTask这个类,AsyncTask内部使用Java 1.5的并发库比普通初级Android开发者编写的Thread+Handler稳定很多
* AsyncTask封装了Thread和Handler，使我们用起来更加方便，不用去关注Handler。由于后台线程不能更新UI，而很多情况下，我们在后台线程做完一件事后，一般都会更新UI，一般我们会这样做：在UI线程创建一个handler对象，在后台线程中发送一条message，再在Handler中去处理这个message，进而更新UI。
* 用了AsyncTask后，我们就不用再去关注Handler了。它定义了三种泛型，分别是Params、Progress和Result，分别表示请求的参数、任务的进度和获得的结果数据。
  
### 使用AsyncTask的好处

    底部封装了线程池技术，其中的方法很容易调用。
    直接调用相关方法，进行UI界面的更新。
    一旦任务多，就不用每次都new新的线程，可以直接使用。  
  
### 实现原理  

* 线程池的创建
	* 在创建了AsyncTask的时候，会默认创建一个线程池ThreadPoolExecutor，并默认创建出5个线程放入到线程池中，最多可防128个线程；且这个线程池是公共的唯一一份。
	
* 任务的执行
	* 在execute中，会执行run方法，当执行完run方法后，会调用scheduleNext()不断的从双端队列中轮询，获取下一个任务并继续放到一个子线程中执行，直到异步任务执行完毕。

* 消息的处理：
	* 在执行完onPreExecute()方法之后，执行了doInBackground()方法，然后就不断的发送请求获取数据；在这个AsyncTask中维护了一个InternalHandler的类，这个类是继承Handler的，获取的数据是通过handler进行处理和发送的。在其handleMessage方法中，将消息传递给onProgressUpdate()进行进度的更新，也就可以将结果发送到主线程中，进行界面的更新了。  
	
### Android中解析XML  
* SAX解析器：
	* SAX(Simple API for XML)解析器是一种基于事件的解析器，它的核心是事件处理模式，主要是围绕着事件源以及事件处理器来工作的。当事件源产生事件后，调用事件处理器相应的处理方法，一个事件就可以得到处理。在事件源调用事件处理器中特定方法的时候，还要传递给事件处理器相应事件的状态信息，这样事件处理器才能够根据提供的事件信息来决定自己的行为。

* SAX解析器的优点是解析速度快，占用内存少。它并不需要解析完整个文档，而是按内容顺序解析文档的过程

* DOM解析器：
	* DOM是基于树形结构的的节点或信息片段的集合，允许开发人员使用DOM API遍历XML树、检索所需数据。分析该结构通常需要加载整个文档和构造树形结构，然后才可以检索和更新节点信息。

* 由于DOM在内存中以树形结构存放，因此检索和更新效率会更高。但是对于特别大的文档，解析和加载整个文档将会很耗资源。

* PULL解析器：
	* PULL解析器的运行方式和SAX类似，都是基于事件的模式。不同的是，在PULL解析过程中，我们需要自己获取产生的事件然后做相应的操作，而不像SAX那样由处理器触发一种事件的方法，执行我们的代码。PULL解析器小巧轻便，解析速度快，简单易用，非常适合在Android移动设备中使用，Android系统内部在解析各种XML时也是用PULL解析器。它提供了类似的事件，可以使用一个switch对感兴趣的事件进行处理