# 面试题目整理 08
<hr>     
  
## Android子线程创建Handler方法   
  
1.方法1（直接获取当前子线程的looper）  
  
如果我们想在子线程上创建Handler，通过直接new的出来是会报异常的比如：   
  
![](https://i.imgur.com/0R2EqjE.jpg)   
   
2.方法2（获取主线程的looper，或者说是UI线程的looper）   
   
![](https://i.imgur.com/LBPpxSA.jpg)   
   
---  
  
## 什么是EventBus   
   
**EventBus能够简化各组件间的通信，让我们的代码书写变得简单，能有效的分离事件发送方和接收方(也就是解耦的意思)，能避免复杂和容易出错的依赖性和生命周期问题。**      
   
EventBus是一款针对Android优化的发布/订阅事件总线。简化了应用程序内各组件间、组件与后台线程间的通信。优点是开销小，代码更优雅，以及将发送者和接收者解耦。如果Activity和Activity进行交互还好说，如果Fragment和Fragment进行交互着实令人头疼，我们会使用广播来处理，但是使用广播稍显麻烦并且效率也不高，如果传递的数据是实体类需要序列化，那么很显然成本会有点高。
   
### EventBus的三要素  
EventBus有三个主要的元素需要我们先了解一下：   

* Event：事件，可以是任意类型的对象。
* Subscriber：事件订阅者，在EventBus3.0之前消息处理的方法只能限定于onEvent、onEventMainThread、onEventBackgroundThread和onEventAsync，他们分别代表四种线程模型。而在EventBus3.0之后，事件处理的方法可以随便取名，但是需要添加一个注解@Subscribe，并且要指定线程模型（默认为POSTING），四种线程模型下面会讲到。
* Publisher：事件发布者，可以在任意线程任意位置发送事件，直接调用EventBus的post(Object)方法。可以自己实例化EventBus对象，但一般使用EventBus.getDefault()就好了，根据post函数参数的类型，会自动调用订阅相应类型事件的函数。   

### EventBus的四种ThreadMode（线程模型）
EventBus3.0有以下四种ThreadMode：

* POSTING（默认）：如果使用事件处理函数指定了线程模型为POSTING，那么该事件在哪个线程发布出来的，事件处理函数就会在这个线程中运行，也就是说发布事件和接收事件在同一个线程。在线程模型为POSTING的事件处理函数中尽量避免执行耗时操作，因为它会阻塞事件的传递，甚至有可能会引起ANR。
* MAIN: 事件的处理会在UI线程中执行。事件处理时间不能太长，长了会ANR的。
* BACKGROUND：如果事件是在UI线程中发布出来的，那么该事件处理函数就会在新的线程中运行，如果事件本来就是子线程中发布出来的，那么该事件处理函数直接在发布事件的线程中执行。在此事件处理函数中禁止进行UI更新操作。
* ASYNC：无论事件在哪个线程发布，该事件处理函数都会在新建的子线程中执行，同样，此事件处理函数中禁止进行UI更新操作。   

### EventBus基本用法
EventBus使用起来很简单，分为五个步骤：   

1.自定义一个事件类   

	public class MessageEvent {
    	...
	}  
   
2.在需要订阅事件的地方注册事件  
  
	EventBus.getDefault().register(this);   
   
3.发送事件   
   
	EventBus.getDefault().post(messageEvent);   
   
4.处理事件   
   
	@Subscribe(threadMode = ThreadMode.MAIN)
	public void XXX(MessageEvent messageEvent) {
    	...
	}   
   
前面我们说过，消息处理的方法可以随便取名，但是需要添加一个注解@Subscribe，并且要指定线程模型（默认为POSTING）。   
   
5.取消事件订阅   
   
	EventBus.getDefault().unregister(this);   
   
---   
   
## EventBus 解决了什么问题
在日常开发中，回调的使用场景非常多，比如按钮的点击事件，网络的请求结果等等，它表示的是对某一将来可能发生事件的监听，具体使用步骤为以下 3 步：

* 创建一个回调接口，在接口中定义监听到事件发生时要进行的操作
* 需要监听的地方创建一个回调的具体实现，然后传递给事件触发者
* 事件触发者持有回调接口的引用，在事件发生时，调用回调接口的具体实现  


非常简单的 3 步就实现了对未来事件的监听。    
   
如果对某一个事件有多个监听，就需要在事件触发者里创建监听者列表，然后在事件发生时挨个通知注册过的监听者。这就是“观察者模式”。   

观察者模式又称“发布-订阅模式”，它用于一个被观察者持有多个观察者对象的引用，当被观察者状态发生改变时，通知所有观察者进行更新。是一种一对多的依赖关系。    
   
## EventBus 的思想   
   
![](https://i.imgur.com/vi13PsG.png)   
   
在 EventBus 中，我们无需实现接口，只要在订阅者中创建监听不同事件的方法，然后使用注解标识。

EventBus 会在编译时和运行时（取决于你是否添加索引）通过处理注解和反射的方式拿到订阅方法和所在的类，然后将订阅者、订阅方法、订阅的事件分别保存在两个属性中。

在有发送者发送事件时，EventBus 根据事件去前面保存的属性里找到订阅者和订阅方法，然后以反射的方式调用它。   
   
