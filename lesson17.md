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
  
