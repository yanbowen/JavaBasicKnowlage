# 面试总结02 
<hr>   
  
## HttpURLConnection与HttpClient浅析
  
* 概念      
	* HTTP 协议可能是现在 Internet 上使用得最多、最重要的协议了，越来越多的 Java 应用程序需要直接通过 HTTP 协议来访问网络资源。在 JDK 的 java.net 包中已经提供了访问 HTTP 协议的基本功能：HttpURLConnection。但是对于大部分应用程序来说，JDK 库本身提供的功能还不够丰富和灵活。
	* 除此之外，在Android中，androidSDK中集成了Apache的HttpClient模块，用来提供高效的、最新的、功能丰富的支持 HTTP 协议工具包，并且它支持 HTTP 协议最新的版本和建议。使用HttpClient可以快速开发出功能强大的Http程序。

* 区别
	* HttpClient是个很不错的开源框架，封装了访问http的请求头，参数，内容体，响应等等，
	* HttpURLConnection是java的标准类，什么都没封装，用起来太原始，不方便，比如重访问的自定义，以及一些高级功能等。  
	 
* 在介绍HttpURLConnection前，我们还是再来说一下URL请求最常用的两种方式：GET请求与POST请求。
	* GET请求的数据会附在URL之后（就是把数据放置在HTTP协议头中），以?分割URL和传输数据，参数之间以&相连，如：http://localhost:8080/test.do?name=test&password=123456。
	* GET请求发送的参数如果数据是英文字母或数字，则按原样发送，如果是空格，则转换为+，如果是中文或其他字符，则直接把字符串用BASE64加密，得出如 %E4%BD%A0%E5%A5%BD 这类似的字符串，其中％XX中的XX为该符号以16进制表示的ASCII。
	* POST请求的参数不是放在URL字符串里面，而是放在HTTP请求的正文内，请求的参数被封装起来以流的形式发送给服务端。
	* 对于GET方式提交数据的大小，HTTP协议并没有硬性限制，但某些浏览器及服务器会对它进行限制，如IE对URL长度的限制是2083字节(2K+35)。理论上POST也没有限制，可传较大量的数据。
	* POST的安全性要比GET的安全性高。比如：通过GET提交数据，用户名和密码将明文出现在URL上，因为登录页面有可能被浏览器缓存，如果其他人查看浏览器的历史纪录，那么别人就可以拿到你的账号和密码了，除此之外，使用GET提交数据还可能会造成Cross-site request forgery（CSRF，跨站请求伪造）攻击。

一般来说，Get是向服务器索取数据的一种请求，而Post是向服务器提交数据的一种请求。
	
### 使用GET方式访问HTTP
		
		/**
		 * GET请求示例
		 */
	public class GetDemo {

    	public static void main(String[] args) {
    	    try {
    	        // 1. 得到访问地址的URL
    	        URL url = new URL(
    	                "http://localhost:8080/Servlet/do_login.do?username=test&password=123456");
    	        // 2. 得到网络访问对象java.net.HttpURLConnection
    	        HttpURLConnection connection = (HttpURLConnection) url
    	                .openConnection();
    	        /* 3. 设置请求参数（过期时间，输入、输出流、访问方式），以流的形式进行连接 */
    	        // 设置是否向HttpURLConnection输出
    	        connection.setDoOutput(false);
    	        // 设置是否从httpUrlConnection读入
    	        connection.setDoInput(true);
    	        // 设置请求方式
    	        connection.setRequestMethod("GET");
    	        // 设置是否使用缓存
    	        connection.setUseCaches(true);
    	        // 设置此 HttpURLConnection 实例是否应该自动执行 HTTP 重定向
    	        connection.setInstanceFollowRedirects(true);
    	        // 设置超时时间
    	        connection.setConnectTimeout(3000);
    	        // 连接
    	        connection.connect();
    	        // 4. 得到响应状态码的返回值 responseCode
    	        int code = connection.getResponseCode();
    	        // 5. 如果返回值正常，数据在网络中是以流的形式得到服务端返回的数据
    	        String msg = "";
    	        if (code == 200) { // 正常响应
                // 从流中读取响应信息
    	            BufferedReader reader = new BufferedReader(
    	                    new InputStreamReader(connection.getInputStream()));
    	            String line = null;
	
    	            while ((line = reader.readLine()) != null) { // 循环从流中读取
    	                msg += line + "\n";
    	            }
    	            reader.close(); // 关闭流
    	        }
    	        // 6. 断开连接，释放资源
    	        connection.disconnect();
	
    	        // 显示响应结果
    	        System.out.println(msg);
    	    } catch (IOException e) {
    	        e.printStackTrace();
    	    }
    	}
	}  
  
## TCP/IP、Socket、Http简要介绍

　　1、TCP/IP中文名为传输控制协议/因特网互联协议，又名网络通讯协议，是Internet最基本的协议、Internet国际互联网络的基础，由网络层的IP协议和传输层的TCP协议组成。

　　2、Socket是支持TCP/IP协议的网络通信基本操作单元，许多操作系统为应用程序提供了一套调用接口（API)，方便开发者开发网络程序。注意，socket本身并不是协议，只是提供一个针对TCP或UDP的编程接口。

　　3、HTTP协议是一个web服务器和客户端通信的超文本传送协议，是建立在TCP协议上的一个应用层协议。HTTP连接最显著的特点是客户端发送的每次请求都需要服务器回送响应，在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为“一次连接”。

* Http Client
	* Apache公司提供的库，提供高效的、最新的、功能丰富的支持HTTP协议工具包，支持HTTP协议最新的版本和建议，是个很不错的开源框架，封装了Http的请求，参数，内容体，响应等，拥有众多API。

* HttpURLConnection
	* Sun公司提供的库，也是Java的标准类库java.net中的一员，但这个类什么都没封装，用起来很原始，若需要高级功能，则会显得不太方便，比如重访问的自定义，会话和cookie等一些高级功能。

HttpURLConnection直接支持GZIP压缩，默认添加"Accept-Encoding: gzip"头字段到请求中，并处理相应的回应，而Http Client虽然支持，但需要自己写代码处理。

## 1. 什么是Activity? 
	四大组件之一,一般的,一个用户交互界面对应一个activity
	setContentView() ,// 要显示的布局 
	activity 是Context的子类,同时实现了window.callback和keyevent.callback, 可以处理与窗体用户交互的事件. 

	我开发常用的的有ListActivity  , PreferenceActivity ,TabAcitivty等…
	如果界面有共同的特点或者功能的时候,还会自己定义一个BaseActivity. 
  
## 2. Android生命周期  
![](https://i.imgur.com/KnHQ1dL.png)   
  
* onCreate : 该方法是在Activity被创建时回调，它是生命周期第一个调用的方法，我们在创建Activity时一般都需要重写该方法，然后在该方法中做一些初始化的操作，如通过setContentView设置界面布局的资源，初始化所需要的组件信息等。 

* onStart : 此方法被回调时表示Activity正在启动，此时Activity已处于可见状态，只是还没有在前台显示，因此无法与用户进行交互。可以简单理解为Activity已显示而我们无法看见摆了。

* onResume : 当此方法回调时，则说明Activity已在前台可见，可与用户交互了（处于前面所说的Active/Running形态），onResume方法与onStart的相同点是两者都表示Activity可见，只不过onStart回调时Activity还是后台无法与用户交互，而onResume则已显示在前台，可与用户交互。当然从流程图，我们也可以看出当Activity停止后（onPause方法和onStop方法被调用），重新回到前台时也会调用onResume方法，因此我们也可以在onResume方法中初始化一些资源，比如重新初始化在onPause或者onStop方法中释放的资源。

* onPause : 此方法被回调时则表示Activity正在停止（Paused形态），一般情况下onStop方法会紧接着被回调。但通过流程图我们还可以看到一种情况是onPause方法执行后直接执行了onResume方法，这属于比较极端的现象了，这可能是用户操作使当前Activity退居后台后又迅速地再回到到当前的Activity，此时onResume方法就会被回调。当然，在onPause方法中我们可以做一些数据存储或者动画停止或者资源回收的操作，但是不能太耗时，因为这可能会影响到新的Activity的显示——onPause方法执行完成后，新Activity的onResume方法才会被执行。

* onStop : 一般在onPause方法执行完成直接执行，表示Activity即将停止或者完全被覆盖（Stopped形态），此时Activity不可见，仅在后台运行。同样地，在onStop方法可以做一些资源释放的操作（不能太耗时）。
* onRestart :表示Activity正在重新启动，当Activity由不可见变为可见状态时，该方法被回调。这种情况一般是用户打开了一个新的Activity时，当前的Activity就会被暂停（onPause和onStop被执行了），接着又回到当前Activity页面时，onRestart方法就会被回调。

* onDestroy :此时Activity正在被销毁，也是生命周期最后一个执行的方法，一般我们可以在此方法中做一些回收工作和最终的资源释放。 
  
## 3.android四大组件详解

1、activity

（1）一个Activity通常就是一个单独的屏幕（窗口）。

（2）Activity之间通过Intent进行通信。

（3）android应用中每一个Activity都必须要在AndroidManifest.xml配置文件中声明，否则系统将不识别也不执行该Activity。

2、service

（1）service用于在后台完成用户指定的操作。service分为两种：

（a）started（启动）：当应用程序组件（如activity）调用startService()方法启动服务时，服务处于started状态。

（b）bound（绑定）：当应用程序组件调用bindService()方法绑定到服务时，服务处于bound状态。

(2)startService()与bindService()区别：

(a)started service（启动服务）是由其他组件调用startService()方法启动的，这导致服务的onStartCommand()方法被调用。当服务是started状态时，其生命周期与启动它的组件无关，并且可以在后台无限期运行，即使启动服务的组件已经被销毁。因此，服务需要在完成任务后调用stopSelf()方法停止，或者由其他组件调用stopService()方法停止。

(b)使用bindService()方法启用服务，调用者与服务绑定在了一起，调用者一旦退出，服务也就终止，大有“不求同时生，必须同时死”的特点。

(3)开发人员需要在应用程序配置文件中声明全部的service，使用<service></service>标签。

(4)Service通常位于后台运行，它一般不需要与用户交互，因此Service组件没有图形用户界面。Service组件需要继承Service基类。Service组件通常用于为其他组件提供后台服务或监控其他组件的运行状态。

3、content provider

（1）android平台提供了Content Provider使一个应用程序的指定数据集提供给其他应用程序。其他应用可以通过ContentResolver类从该内容提供者中获取或存入数据。

（2）只有需要在多个应用程序间共享数据是才需要内容提供者。例如，通讯录数据被多个应用程序使用，且必须存储在一个内容提供者中。它的好处是统一数据访问方式。

（3）ContentProvider实现数据共享。ContentProvider用于保存和获取数据，并使其对所有应用程序可见。这是不同应用程序间共享数据的唯一方式，因为android没有提供所有应用共同访问的公共存储区。

（4）开发人员不会直接使用ContentProvider类的对象，大多数是通过ContentResolver对象实现对ContentProvider的操作。

（5）ContentProvider使用URI来唯一标识其数据集，这里的URI以content://作为前缀，表示该数据由ContentProvider来管理。

4、broadcast receiver

（1）你的应用可以使用它对外部事件进行过滤，只对感兴趣的外部事件(如当电话呼入时，或者数据网络可用时)进行接收并做出响应。广播接收器没有用户界面。然而，它们可以启动一个activity或serice来响应它们收到的信息，或者用NotificationManager来通知用户。通知可以用很多种方式来吸引用户的注意力，例如闪动背灯、震动、播放声音等。一般来说是在状态栏上放一个持久的图标，用户可以打开它并获取消息。

（2）广播接收者的注册有两种方法，分别是程序动态注册和AndroidManifest文件中进行静态注册。

（3）动态注册广播接收器特点是当用来注册的Activity关掉后，广播也就失效了。静态注册无需担忧广播接收器是否被关闭，只要设备是开启状态，广播接收器也是打开着的。也就是说哪怕app本身未启动，该app订阅的广播在触发时也会对它起作用。  
  
