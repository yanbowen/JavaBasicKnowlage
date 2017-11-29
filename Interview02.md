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
