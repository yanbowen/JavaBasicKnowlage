# Java基础17  
<hr>     
  
## Properties概述  
  
* Properties是hashtable的子类
	* 所以其具备了map集合的特点，而且里面存储的键值对都是字符串
	* 可以用于键值对形式的配置文件  
  
load加载，字节流数据，字符流也可以但是从1.6开始  


		Properties prop = new Properties();
		FileInputStream fis = new FileInputStream("info.txt");
			
		//将流中的数据加载进集合
		prop.load(fis);
		System.out.println(prop);
		prop.list(System.out);
  
		prop.setProperty("wangsx","52");
		FileOutputStream fos = new FileOutputSteam("info.txt");
		prop.store(fos."zhushi");
		
		fis.close();
		fos.close();  
  
---  
  
## 打印流
* 该流提供了打印方法，可以将各种数据类型原样打印
* 字节打印流 ： PrintStream
	* file对象 File
	* 字符串路径 String
	* 字节输出流 OutputStream
* 字符打印流 ： PrintWriter
	* file对象 File
	* 字符串路径 String
	* 字节输出流 OutputStream
	* 字符输出流 Writer  
	  
---  
  
	BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
	PrintWriter out = new PrintWriter(System.out,true);
	String line = null;
	
	while((line=bufr.readLine())!=null)
	{
		if("over".equals(line))
			break;
		out.println(line.toUpperCase());
		//out.flush();
	}
	
	out.close();
	bufr.close();  

---  
  
### IP地址
  
	InetAddress ia = new InteAddress.getByName("www.baidu.com");
	System.out.println("address" + ia.getHostAddress());
	System.out.println("NAME" + ia.getHostName());  
  
有效端口号  

	0 ~ 65535 ， 其中 0 ~ 1024 系统使用或保留端口  
  
### UDP(面向无连接) 
* 将数据及源和目的封装成数据包中，不需要建立连接  
* 每个数据包的大小限制在64k内
* 无连接，不可靠连接、速度快  

UDP过程  

	服务器端
	A：创建发送端Socket对象
	B：创建数据，并把数据打包
	C：调用Socket对象的发送方法发送数据包
	D：释放资源
	
	基于UDP
		// 创建发送端对象
		DatagramSocket ds = null;
		// IP地址对象
		InetAddress address = null;
		// 创建字节数组
		byte[] bys = "hello,UDP".getBytes();
		// 长度
		int length = bys.length;
		// 端口
		int port = 10086;
		try {
			ds = new DatagramSocket();
			address = InetAddress.getByName("127.0.0.1");
			DatagramPacket dp = new DatagramPacket(bys, length, address, port);
			// 调用Socket对象的发送方法发送数据包
			ds.send(dp);
		} catch (Exception e) {
		} finally {
			if (ds != null)
				ds.close();
		}

	客户端
	A：创建接收端Socket对象
	B：创建一个数据包（接收容器）
	C：调用Socket对象的接收方法接收数据
	D：解析数据，并显示控制台
	E：释放资源

		// 创建接收端对象
		DatagramSocket ds = null;
		byte[] bys = new byte[1024];
		int length = bys.length;
		try {
			DatagramPacket dp = new DatagramPacket(bys, length);
			ds = new DatagramSocket(10086);
			ds.receive(dp);// 阻塞方法
			InetAddress address = dp.getAddress();
			String ip = address.getHostAddress();
			byte[] by2 = dp.getData();
			int len = dp.getLength();
			String s = new String(by2, 0, len);
			System.out.println(ip + ":" + s);
		} catch (Exception e) {
		} finally {
			if (ds != null)
				ds.close();
		}
  
### TCP  
* 建立连接，形成传输数据的通道
* 通过三次握手完成连接，可靠协议
* 必须建立连接，功率会稍低    
  
![](https://i.imgur.com/Wq37XAE.png)  
  
#### TCP连接时是三次握手，那么两次握手可行吗？

* 在《计算机网络》中是这样解释的：已失效的连接请求报文段”的产生在这样一种情况下：client发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达server。本来这是一个早已失效的报文段。但server收到此失效的连接请求报文段后，就误认为是client再次发出的一个新的连接请求。于是就向client发出确认报文段，同意建立连接。假设不采用“三次握手”，那么只要server发出确认，新的连接就建立了。由于现在client并没有发出建立连接的请求，因此不会理睬server的确认，也不会向server发送ACK包。这样就会白白浪费资源。

* 而经过三次握手，客户端和服务器都有应有答，这样可以确保TCP正确连接。


### Socket (套接字编程)
* Socket为网络服务的一种机制
* 通信两端都有Socket
* 数据在两个Socket间通过IO传输  


 
### java创建对象几种方式
	
1.	使用new关键字：这是我们最常见的也是最简单的创建对象的方式，通过这种方式我们还可以调用任意的够赞函数（无参的和有参的）。比如：Student student = new Student();

2.	使用Class类的newInstance方法：我们也可以使用Class类的newInstance方法创建对象，这个newInstance方法调用无参的构造器创建对象，如：Student student2 = (Student)Class.forName("根路径.Student").newInstance();　或者：Student stu = Student.class.newInstance();  

3.	使用Constructor类的newInstance方法：本方法和Class类的newInstance方法很像，java.lang.relect.Constructor类里也有一个newInstance方法可以创建对象。我们可以通过这个newInstance方法调用有参数的和私有的构造函数。如： Constructor<Student> constructor = Student.class.getInstance(); Student stu = constructor.newInstance();　这两种newInstance的方法就是大家所说的反射，事实上Class的newInstance方法内部调用Constructor的newInstance方法。这也是众多框架Spring、Hibernate、Struts等使用后者的原因。  

4.	使用Clone的方法：无论何时我们调用一个对象的clone方法，JVM就会创建一个新的对象，将前面的对象的内容全部拷贝进去，用clone方法创建对象并不会调用任何构造函数。要使用clone方法，我们必须先实现Cloneable接口并实现其定义的clone方法。如：Student stu2 = <Student>stu.clone();这也是原型模式的应用。  

5.	使用反序列化：当我们序列化和反序列化一个对象，JVM会给我们创建一个单独的对象，在反序列化时，JVM创建对象并不会调用任何构造函数。为了反序列化一个对象，我们需要让我们的类实现Serializable接口。如：ObjectInputStream in = new ObjectInputStream (new FileInputStream("data.obj")); Student stu3 = (Student)in.readObject();  
  
---  
  
## 网络模型

### OSI (Open System Interconnection 开放系统互连)  
![](https://i.imgur.com/i7xrSwf.png)  

* 应用层 ：  OSI参考模型中最靠近用户的一层，是为计算机用户提供应用接口，也为用户直接提供各种网络服务。我们常见应用层的网络服务协议有：HTTP，HTTPS，FTP，POP3、SMTP等。
* 表示层 ：  表示层提供各种用于应用层数据的编码和转换功能,确保一个系统的应用层发送的数据能被另一个系统的应用层识别。如果必要，该层可提供一种标准表示形式，用于将计算机内部的多种数据格式转换成通信中采用的标准表示形式。数据压缩和加密也是表示层可提供的转换功能之一。
* 会话层 ：  会话层就是负责建立、管理和终止表示层实体之间的通信会话。该层的通信由不同设备中的应用程序之间的服务请求和响应组成。
* 传输层 ：  传输层建立了主机端到端的链接，传输层的作用是为上层协议提供端到端的可靠和透明的数据传输服务，包括处理差错控制和流量控制等问题。该层向高层屏蔽了下层数据通信的细节，使高层用户看到的只是在两个传输实体间的一条主机到主机的、可由用户控制和设定的、可靠的数据通路。我们通常说的，TCP UDP就是在这一层。端口号既是这里的“端”。
* 网络层 ：  本层通过IP寻址来建立两个节点之间的连接，为源端的运输层送来的分组，选择合适的路由和交换节点，正确无误地按照地址传送给目的端的运输层。就是通常说的IP层。这一层就是我们经常说的IP协议层。IP协议是Internet的基础。
* 数据链路层 ：  将比特组合成字节,再将字节组合成帧,使用链路层地址 (以太网使用MAC地址)来访问介质,并进行差错检测。
* 物理层 ：  实际最终信号的传输是通过物理层实现的。通过物理介质传输比特流。规定了电平、速度和电缆针脚。常用设备有（各种物理设备）集线器、中继器、调制解调器、网线、双绞线、同轴电缆。这些都是物理层的传输介质。
  
## 网络编程三要素

* IP地址
* 端口
* 协议

### IP地址 
	IP	：	192.168.1.100
	换算	：	11000000 10101000 00000001 011001000
  
	A类 ： 前一号段网络号段 + 后三段主机号段
	1.0.0.1 - 127.255.255.254	   共有： 256*256*256=16777216
	
	B类 ： 前二号段网络号段 + 后二段主机号段
	128.0.0.1 - 191.255.255.254	   共有： 256*256=65536
	
	C类 ： 前三号段网络号段 + 后一段主机号段
	192.0.0.1 - 223.255.255.254	   共有： 256
	
	D类 ： 224.0.0.1 - 239.255.255.254
	E类 ： 240.0.0.1 - 247.255.255.254
 
	10.X.X.X 是私有地址
	192.168.X.X 是私有地址
	127.0.0.1	回环地址
	
	X.X.X.255  广播地址	不建议使用
	X.X.X.0	   网络地址	不建议使用
	
### InetAddress类	

	InetAddress address=InetAddress.getByName("141.146.8.66");  
	System.out.println(address.getHostName());//需要访问DNS服务器才能得到域名  
  
