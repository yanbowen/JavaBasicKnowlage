# 面试题目整理 05
<hr>   

## Android Framework层理解
Framework定义了客户端组件和服务端组件功能及接口。包含3个主要部分：服务端，客户端和Linux驱动。  
  
一：服务端

服务端主要包含两个重要类，分别是WindowManagerService(Wms)和ActivityManagerService(Ams)。Wms的作用是为所有的应用程序分配窗口，并管理这些窗口。包括分配窗口大小，调节各窗口的叠放次序，隐藏或显示窗口。Ams的作用是管理所有应用程序中的Activity。

除此之外，在服务端还包括2个消息处理类：
1）KeyQ类：

该类为Wms的内部类，继承于KeyInputQueue类， KeyQ对象一旦创建，就立即启动一个线程，该线程会不断地读取用户的UI操作消息，比如按键、触摸屏、trackball、鼠标等，并把这些消息放到一个消息队列QueueEvent类中。

2）InputDispatcherThread类：

该类的对象一旦创建，也会立即启动一个线程，该线程会不断地从QueueEvent中取出用户消息，并进行一定的过滤（用Wms中相应函数处理），过滤后，再将这些消息发给当前活动的客户端程序中。  
  
二：客户端

  客户端主要包括以下重要类：

1）ActivityThread类：

    该类为应用程序的主线程类，所有的Apk程序有且只有一个ActivityThread类，程序的入口为该类中的static main()函数。
2）Activity类：

    该类为APK程序的一个最小运行单元，一个APK程序中可以包含多个Activity对象，ActivityThread主类会根据用户操作选择运行哪个Activity对象。
3）PhoneWindow类：

    该类继承与Window类，同时，PhoneWindow类内部包含了一个DecorView对象。简而言之，PhoneWindow是把一个FrameLayout进行了一定的包装，并提供了一组通用的窗口操作接口。
4）Window类：

    该类提供了一组通用的窗口（Window）操作API, 这里的窗口仅仅是程序层面上的，Wms所管理的窗口并不是Window类，而是一个View或者ViewGroup类，一般就是指DecorView类，即一个DecorView就是Wms所有管理的一个窗口。Window是一个abstract类型。

5）DecorView类：

    该类是一个FrameLayout的子类，并且是PhoneWindow中的一个内部类。Decor的英文是Decoration, 即“修饰”的意思，DecorView就是对普通的FrameLayout进行了一定的修饰，比如添加一个通用的TitleBar, 并响应特定的按键消息等。

6）ViewRoot类： 

    Wms管理客户端窗口时，需要通知客户端进行某种操作，这些都是通过异步消息完成的，实现方式就是使用Handler, ViewRoot就是继承于Handler，其作用主要是接收Wms的通知。

7）W类：

    该类继承于Binder, 并且是ViewRoot的一个内部类。

8）WindowManager类：

    客户端要申请创建一个窗口，而具体创建窗口的任务是由Wms完成的，WindowManager类就像是一个部门经理，谁有什么需求就告诉它，由它和Wms进行交互，客户端不能直接和Wms进行交互。  
   
三：Linux驱动

Linux驱动和Framework相关的主要包含两部分，分别是SurfaceFlingger(SF)和Binder。每一个窗口都对应一个Surface, SF驱动的作用是把各个Surface显示在同一屏幕上。Binder驱动的作用是提供跨进程（IPC)的消息传递机制。  
  
---  
  
  
## Android多线程的四种方式  
  
https://www.jianshu.com/p/2b634a7c49ec  
  
当我们启动一个App的时候，Android系统会启动一个Linux Process，该Process包含一个Thread，称为UI Thread或Main Thread。通常一个应用的所有组件都运行在这一个Process中，当然，你可以通过修改四大组件在Manifest.xml中的代码块(<activity><service><provider><receiver>)中的android:process属性指定其运行在不同的process中。当一个组件在启动的时候，如果该process已经存在了，那么该组件就直接通过这个process被启动起来，并且运行在这个process的UI Thread中。

UI Thread中运行着许多重要的逻辑，如系统事件处理，用户输入事件处理，UI绘制，Service，Alarm等，如下图：
  
![](https://i.imgur.com/59VtF7F.png)  
  
Android提供了四种常用的操作多线程的方式，分别是：  
1. Handler+Thread  
2. AsyncTask  
3. ThreadPoolExecutor  
4. IntentService  


