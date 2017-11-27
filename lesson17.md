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
  
### TCP  
* 建立连接，形成传输数据的通道
* 通过三次握手完成连接，可靠协议
* 必须建立连接，功率会稍低  

### Socket
* Socket为网络服务的一种机制
* 通信两端都有Socket
* 数据在两个Socket间通过IO传输