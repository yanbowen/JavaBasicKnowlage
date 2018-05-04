# 面试总结02 
<hr>   
  
## HttpURLConnection与HttpClient浅析
  
### 1. GET请求与POST请求

HTTP协议是现在Internet上使用得最多、最重要的协议了，越来越多的Java应用程序需要直接通过HTTP协议来访问网络资源。

在介绍HttpURLConnection前，我们还是再来说一下URL请求最常用的两种方式：GET请求与POST请求。

GET请求的数据会附在URL之后（就是把数据放置在HTTP协议头中），以?分割URL和传输数据，参数之间以&相连，如：http://localhost:8080/test.do?name=test&password=123456。

GET请求发送的参数如果数据是英文字母或数字，则按原样发送，如果是空格，则转换为+，如果是中文或其他字符，则直接把字符串用BASE64加密，得出如 %E4%BD%A0%E5%A5%BD 这类似的字符串，其中％XX中的XX为该符号以16进制表示的ASCII。

POST请求的参数不是放在URL字符串里面，而是放在HTTP请求的正文内，请求的参数被封装起来以流的形式发送给服务端。

对于GET方式提交数据的大小，HTTP协议并没有硬性限制，但某些浏览器及服务器会对它进行限制，如IE对URL长度的限制是2083字节(2K+35)。理论上POST也没有限制，可传较大量的数据。

POST的安全性要比GET的安全性高。比如：通过GET提交数据，用户名和密码将明文出现在URL上，因为登录页面有可能被浏览器缓存，如果其他人查看浏览器的历史纪录，那么别人就可以拿到你的账号和密码了，除此之外，使用GET提交数据还可能会造成Cross-site request forgery（CSRF，跨站请求伪造）攻击。

一般来说，Get是向服务器索取数据的一种请求，而Post是向服务器提交数据的一种请求。    
    
### 2. HttpURLConnection简介

在JDK的java.net包中已经提供了访问HTTP协议的基本功能的类：HttpURLConnection。

HttpURLConnection是Java的标准类，它继承自URLConnection，可用于向指定网站发送GET请求、POST请求。它在URLConnection的基础上提供了如下便捷的方法：
	 
	int getResponseCode(); // 获取服务器的响应代码。
	String getResponseMessage(); // 获取服务器的响应消息。
	String getResponseMethod(); // 获取发送请求的方法。
	void setRequestMethod(String method); // 设置发送请求的方法。
     
### 3. HttpURLConnection的使用

#### 3.1 使用GET方式访问HTTP   
   
	package com.qf.demo;
	
	import java.io.BufferedReader;
	import java.io.IOException;
	import java.io.InputStreamReader;
	import java.net.HttpURLConnection;
	import java.net.URL;
	
	/**
	 * GET请求示例
	 * 
	 *
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
    
#### 3.2 使用POST方式访问HTTP   
   
	package com.qf.demo;
	
	import java.io.BufferedReader;
	import java.io.IOException;
	import java.io.InputStreamReader;
	import java.io.OutputStream;
	import java.net.HttpURLConnection;
	import java.net.MalformedURLException;
	import java.net.URL;
	
	/**
	 * POST请求示例
	 * 
	 * @author 小明
	 *
	 */
	public class PostDemo {
	
	    public static void main(String[] args) {
	        try {
	            // 1. 获取访问地址URL
	            URL url = new URL("http://localhost:8080/Servlet/do_login.do");
	            // 2. 创建HttpURLConnection对象
	            HttpURLConnection connection = (HttpURLConnection) url
	                    .openConnection();
	            /* 3. 设置请求参数等 */
	            // 请求方式
	            connection.setRequestMethod("POST");
	            // 超时时间
	            connection.setConnectTimeout(3000);
	            // 设置是否输出
	            connection.setDoOutput(true);
	            // 设置是否读入
	            connection.setDoInput(true);
	            // 设置是否使用缓存
	            connection.setUseCaches(false);
	            // 设置此 HttpURLConnection 实例是否应该自动执行 HTTP 重定向
	            connection.setInstanceFollowRedirects(true);
	            // 设置使用标准编码格式编码参数的名-值对
	            connection.setRequestProperty("Content-Type",
	                    "application/x-www-form-urlencoded");
	            // 连接
	            connection.connect();
	            /* 4. 处理输入输出 */
	            // 写入参数到请求中
	            String params = "username=test&password=123456";
	            OutputStream out = connection.getOutputStream();
	            out.write(params.getBytes());
	            out.flush();
	            out.close();
	            // 从连接中读取响应信息
	            String msg = "";
	            int code = connection.getResponseCode();
	            if (code == 200) {
	                BufferedReader reader = new BufferedReader(
	                        new InputStreamReader(connection.getInputStream()));
	                String line;
	
	                while ((line = reader.readLine()) != null) {
	                    msg += line + "\n";
	                }
	                reader.close();
	            }
	            // 5. 断开连接
	            connection.disconnect();
	
	            // 处理结果
	            System.out.println(msg);
	        } catch (MalformedURLException e) {
	            e.printStackTrace();
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	}    
    
#### 3.3 说明

* HttpURLConnection对象不能直接构造，需要通过URL类中的openConnection()方法来获得。
* HttpURLConnection的connect()函数，实际上只是建立了一个与服务器的TCP连接，并没有实际发送HTTP请求。HTTP请求实际上直到我们获取服务器响应数据（如调用getInputStream()、getResponseCode()等方法）时才正式发送出去。
* 对HttpURLConnection对象的配置都需要在connect()方法执行之前完成。
* HttpURLConnection是基于HTTP协议的，其底层通过socket通信实现。如果不设置超时（timeout），在网络异常的情况下，可能会导致程序僵死而不继续往下执行。
* HTTP正文的内容是通过OutputStream流写入的， 向流中写入的数据不会立即发送到网络，而是存在于内存缓冲区中，待流关闭时，根据写入的内容生成HTTP正文。
* 调用getInputStream()方法时，返回一个输入流，用于从中读取服务器对于HTTP请求的返回信息。
* 我们可以使用HttpURLConnection.connect()方法手动的发送一个HTTP请求，但是如果要获取HTTP响应的时候，请求就会自动的发起，比如我们使用HttpURLConnection.getInputStream()方法的时候，所以完全没有必要调用connect()方法。   

### 4. HttpClient简介

在一般情况下，如果只是需要向Web站点的某个简单页面提交请求并获取服务器响应，HttpURLConnection完全可以胜任。但在绝大部分情况下，Web站点的网页可能没这么简单，这些页面并不是通过一个简单的URL就可访问的，可能需要用户登录而且具有相应的权限才可访问该页面。在这种情况下，就需要涉及Session、Cookie的处理了，如果打算使用HttpURLConnection来处理这些细节，当然也是可能实现的，只是处理起来难度就大了。

为了更好地处理向Web站点请求，包括处理Session、Cookie等细节问题，Apache开源组织提供了一个HttpClient项目，看它的名称就知道，它是一个简单的HTTP客户端（并不是浏览器），可以用于发送HTTP请求，接收HTTP响应。但不会缓存服务器的响应，不能执行HTML页面中嵌入的Javascript代码；也不会对页面内容进行任何解析、处理。

简单来说，HttpClient就是一个增强版的HttpURLConnection，HttpURLConnection可以做的事情HttpClient全部可以做；HttpURLConnection没有提供的有些功能，HttpClient也提供了，但它只是关注于如何发送请求、接收响应，以及管理HTTP连接。      
   
### 5. HttpClient的使用

使用HttpClient发送请求、接收响应很简单，只要如下几步即可。

1. 创建HttpClient对象。
2. 如果需要发送GET请求，创建HttpGet对象；如果需要发送POST请求，创建HttpPost对象。
3. 如果需要发送请求参数，可调用HttpGet、HttpPost共同的setParams(HttpParams params)方法来添加请求参数；对于HttpPost对象而言，也可调用setEntity(HttpEntity entity)方法来设置请求参数。
4. 调用HttpClient对象的execute(HttpUriRequest request)发送请求，执行该方法返回一个HttpResponse。
5. 调用HttpResponse的getAllHeaders()、getHeaders(String name)等方法可获取服务器的响应头；调用HttpResponse的getEntity()方法可获取HttpEntity对象，该对象包装了服务器的响应内容。程序可通过该对象获取服务器的响应内容。
   
#### 5.1 使用GET方式访问HTTP    
   
	package com.qf.client;
	
	import java.io.IOException;
	
	import org.apache.http.HttpEntity;
	import org.apache.http.client.ClientProtocolException;
	import org.apache.http.client.methods.CloseableHttpResponse;
	import org.apache.http.client.methods.HttpGet;
	import org.apache.http.impl.client.CloseableHttpClient;
	import org.apache.http.impl.client.HttpClientBuilder;
	import org.apache.http.util.EntityUtils;
	
	/**
	 * GET请求示例
	 * 
	 * @author 小明
	 *
	 */
	public class GetDemo {
	
	    public static void main(String[] args) {
	        // 1. 创建HttpClient对象
	        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
	        // 2. 创建HttpGet对象
	        HttpGet httpGet = new HttpGet(
	                "http://localhost:8080/Servlet/do_login.do?username=test&password=123456");
	        CloseableHttpResponse response = null;
	        try {
	            // 3. 执行GET请求
	            response = httpClient.execute(httpGet);
	            System.out.println(response.getStatusLine());
	            // 4. 获取响应实体
	            HttpEntity entity = response.getEntity();
	            // 5. 处理响应实体
	            if (entity != null) {
	                System.out.println("长度：" + entity.getContentLength());
	                System.out.println("内容：" + EntityUtils.toString(entity));
	            }
	        } catch (ClientProtocolException e) {
	            e.printStackTrace();
	        } catch (IOException e) {
	            e.printStackTrace();
	        } finally {
	            // 6. 释放资源
	            try {
	                response.close();
	                httpClient.close();
	            } catch (IOException e) {
	                e.printStackTrace();
	            }
	        }
	    }
	}
    
#### 5.2 使用POST方式访问HTTP    
   
	package com.qf.client;
	
	import java.io.IOException;
	import java.io.UnsupportedEncodingException;
	import java.util.ArrayList;
	import java.util.List;
	
	import org.apache.http.HttpEntity;
	import org.apache.http.NameValuePair;
	import org.apache.http.client.ClientProtocolException;
	import org.apache.http.client.entity.UrlEncodedFormEntity;
	import org.apache.http.client.methods.CloseableHttpResponse;
	import org.apache.http.client.methods.HttpPost;
	import org.apache.http.impl.client.CloseableHttpClient;
	import org.apache.http.impl.client.HttpClientBuilder;
	import org.apache.http.message.BasicNameValuePair;
	import org.apache.http.util.EntityUtils;
	
	/**
	 * POST请求测试
	 * 
	 * @author 小明
	 *
	 */
	public class PostDemo {
	
	    public static void main(String[] args) {
	        // 1. 创建HttpClient对象
	        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
	        // 2. 创建HttpPost对象
	        HttpPost post = new HttpPost(
	                "http://localhost:8080/Servlet/do_login.do");
	        // 3. 设置POST请求传递参数
	        List<NameValuePair> params = new ArrayList<NameValuePair>();
	        params.add(new BasicNameValuePair("username", "test"));
	        params.add(new BasicNameValuePair("password", "12356"));
	        try {
	            UrlEncodedFormEntity entity = new UrlEncodedFormEntity(params);
	            post.setEntity(entity);
	        } catch (UnsupportedEncodingException e) {
	            e.printStackTrace();
	        }
	        // 4. 执行请求并处理响应
	        try {
	            CloseableHttpResponse response = httpClient.execute(post);
	            HttpEntity entity = response.getEntity();
	            if (entity != null){
	                System.out.println("响应内容：");
	                System.out.println(EntityUtils.toString(entity));
	            }
	            response.close();
	        } catch (ClientProtocolException e) {
	            e.printStackTrace();
	        } catch (IOException e) {
	            e.printStackTrace();
	        } finally {
	            // 释放资源
	            try {
	                httpClient.close();
	            } catch (IOException e) {
	                e.printStackTrace();
	            }
	        }
	    }
	}
     
  
#### 5.3 说明

HttpClient相比传统JDK自带的URLConnection，增加了易用性和灵活性，它不仅使客户端发送HTTP请求变得容易，而且也方便了开发人员测试接口（基于Http协议的），即提高了开发的效率，也方便提高代码的健壮性。   


## TCP/IP、Socket、Http简要介绍

　　1、TCP/IP中文名为传输控制协议/因特网互联协议，又名网络通讯协议，是Internet最基本的协议、Internet国际互联网络的基础，由网络层的IP协议和传输层的TCP协议组成。

　　2、Socket是支持TCP/IP协议的网络通信基本操作单元，许多操作系统为应用程序提供了一套调用接口（API)，方便开发者开发网络程序。注意，socket本身并不是协议，只是提供一个针对TCP或UDP的编程接口。

　　3、HTTP协议是一个web服务器和客户端通信的超文本传送协议，是建立在TCP协议上的一个应用层协议。HTTP连接最显著的特点是客户端发送的每次请求都需要服务器回送响应，在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为“一次连接”。

* Http Client
	* Apache公司提供的库，提供高效的、最新的、功能丰富的支持HTTP协议工具包，支持HTTP协议最新的版本和建议，是个很不错的开源框架，封装了Http的请求，参数，内容体，响应等，拥有众多API。

* HttpURLConnection
	* Sun公司提供的库，也是Java的标准类库java.net中的一员，但这个类什么都没封装，用起来很原始，若需要高级功能，则会显得不太方便，比如重访问的自定义，会话和cookie等一些高级功能。

HttpURLConnection直接支持GZIP压缩，默认添加"Accept-Encoding: gzip"头字段到请求中，并处理相应的回应，而Http Client虽然支持，但需要自己写代码处理。

	
## 基于Socket的java网络编程

1，什么是Socket

网络上的两个程序通过一个双向的通讯连接实现数据的交换，这个双向链路的一端称为一个Socket。Socket通常用来实现客户方和服务方的连接。Socket是TCP/IP协议的一个十分流行的编程界面，一个Socket由一个IP地址和一个端口号唯一确定。

但是，Socket所支持的协议种类也不光TCP/IP一种，因此两者之间是没有必然联系的。在Java环境下，Socket编程主要是指基于TCP/IP协议的网络编程。

2，Socket通讯的过程

Server端Listen(监听)某个端口是否有连接请求，Client端向Server 端发出Connect(连接)请求，Server端向Client端发回Accept（接受）消息。一个连接就建立起来了。Server端和Client 端都可以通过Send，Write等方法与对方通信。

对于一个功能齐全的Socket，都要包含以下基本结构，其工作过程包含以下四个基本的步骤：

　　（1） 创建Socket；

　　（2） 打开连接到Socket的输入/出流；

　　（3） 按照一定的协议对Socket进行读/写操作；

　　（4） 关闭Socket.（在实际应用中，并未使用到显示的close，虽然很多文章都推荐如此，不过在我的程序中，可能因为程序本身比较简单，要求不高，所以并未造成什么影响。）

----

## 简单的Client/Server程序 

	客户端程序
	import java.io.*;
	import java.net.*;

	public class TalkClient {
		public static void main(String args[]) {

			try{
				Socket socket=new Socket("127.0.0.1",4700);
				//向本机的4700端口发出客户请求
				BufferedReader sin=new BufferedReader(new InputStreamReader(System.in));
				
				//由系统标准输入设备构造BufferedReader对象
				PrintWriter os=new PrintWriter(socket.getOutputStream());

				//由Socket对象得到输出流，并构造PrintWriter对象
				BufferedReader is=new BufferedReader(new InputStreamReader(socket.getInputStream()));

				//由Socket对象得到输入流，并构造相应的BufferedReader对象
				String readline;

				readline=sin.readLine(); //从系统标准输入读入一字符串
				while(!readline.equals("bye")){
					//若从标准输入读入的字符串为 "bye"则停止循环
					os.println(readline);
					//将从系统标准输入读入的字符串输出到Server
					os.flush();

					//刷新输出流，使Server马上收到该字符串
					System.out.println("Client:"+readline);
					//在系统标准输出上打印读入的字符串
					System.out.println("Server:"+is.readLine());
					//从Server读入一字符串，并打印到标准输出上
					readline=sin.readLine(); //从系统标准输入读入一字符串

				} //继续循环
				os.close(); //关闭Socket输出流
				is.close(); //关闭Socket输入流
				socket.close(); //关闭Socket

			}catch(Exception e) {
				System.out.println("Error"+e); //出错，则打印出错信息
			}
		}
	}



	服务器端程序
	import java.io.*;
	import java.net.*;
	import java.applet.Applet;

	public class TalkServer{
		public static void main(String args[]) {
			try{
				ServerSocket server=null;
				try{
					server=new ServerSocket(4700);//创建一个ServerSocket在端口4700监听客户请求
				}catch(Exception e) {
					System.out.println("can not listen to:"+e);
				//出错，打印出错信息

				}
				Socket socket=null;
			try{
				socket=server.accept();//使用accept()阻塞等待客户请求，有客户
				//请求到来则产生一个Socket对象，并继续执行
				}catch(Exception e) {
				System.out.println("Error."+e);
				//出错，打印出错信息
				}
				String line;
				//由Socket对象得到输入流，并构造相应的BufferedReader对象
				BufferedReader is=new BufferedReader(new InputStreamReader(socket.getInputStream()));

				//由Socket对象得到输出流，并构造PrintWriter对象
				PrintWriter os=newPrintWriter(socket.getOutputStream());

				//由系统标准输入设备构造BufferedReader对象
				BufferedReader sin=new BufferedReader(new InputStreamReader(System.in));

				//在标准输出上打印从客户端读入的字符串
				System.out.println("Client:"+is.readLine());


				//从标准输入读入一字符串
				line=sin.readLine();

				//如果该字符串为 "bye"，则停止循环
				while(!line.equals("bye")){
					os.println(line);
					//向客户端输出该字符串

					os.flush();
					//刷新输出流，使Client马上收到该字符串

					System.out.println("Server:"+line);
					//在系统标准输出上打印读入的字符串

					System.out.println("Client:"+is.readLine());
					//从Client读入一字符串，并打印到标准输出上

					line=sin.readLine();
					//从系统标准输入读入一字符串
				} 　//继续循环

				os.close(); //关闭Socket输出流
				is.close(); //关闭Socket输入流

				socket.close(); //关闭Socket
				server.close(); //关闭ServerSocket	(不应该关闭)

			}catch(Exception e){
				System.out.println("Error:"+e);
				//出错，打印出错信息

			}
		}
	}

## 支持多客户的client/server程序

前面的Client/Server程序只能实现Server和一个客户的对话。在实际应用 中，往往是在服务器上运行一个永久的程序，它可以接收来自其他多个客户端的请求，提供相应的服务。为了实现在服务器方给多个客户提供服务的功能，需要对上 面的程序进行改造，利用多线程实现多客户机制。服务器总是在指定的端口上监听是否有客户请求，一旦监听到客户请求，服务器就会启动一个专门的服务线程来响 应该客户的请求，而服务器本身在启动完线程之后马上又进入监听状态，等待下一个客户的到来。

---
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
  
