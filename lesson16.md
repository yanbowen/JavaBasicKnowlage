# Java基础16  
<hr>     
  
## 读取键盘录入  
* FileReader
* FileWriter
* BufferedReader
* BufferedWriter
    
---
* FileInputStream
* FileOutputStream
* BufferedInputStream
* BufferedOutputStream  

---  
  
readLine方法是字符流BufferedReader类中的方法   

类 InputStreamReader  ：InputStreamReader 是字节流通向字符流的桥梁
  

	class ReadIn
	{
		public static void main(String[] args)
		{
			InputStream in = System.in;
			//将字节流对象转换成字符流对象
			InputStreamReader isr = new InputStreamReader(in);
			//提高效率，将字符流进行缓冲区
			BufferedReader bufr = new BufferedReader(isr);
			String line = null;
			while((line=bufr.readLine())!=null)
			{
				if("over".equals(line))
					break;
				System.out.println(line.toUpperCase());
			}
			bufr.close();
		}
	}  
  
---  
  
类 OutputStreamWriter  ： OutputStreamWriter 是字符流通向字节流的桥梁  
  
	class ReadOut
	{
		public static void main(String[] args)
		{
			InputStream in = System.in;
			//将字节流对象转换成字符流对象
			InputStreamReader isr = new InputStreamReader(in);
			//提高效率，将字符流进行缓冲区
			BufferedReader bufr = new BufferedReader(isr);


			//上述三句话合成一句
			//键盘录入
			BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));

			OutputStream out = System.out;
			OutputStreamWriter osw = new OutputStreamWriter(out);
			BufferedWriter bufw = new BufferedWriter(osw);


			//上述三句话合成一句
			


			String line = null;
			while((line=bufr.readLine())!=null)
			{
				if("over".equals(line))
					break;
				bufw.write(line.toUpperCase());
				bufw.newLine();
				bufw.flush();
			}
			bufr.close();
		}
	}  
  
---  
  
## 异常日志信息  
  
	import java.io.*;
	import java.text.*;
	import java.util.*;
	
	class ExceptionInfo
	{
		try
		{
			int[] arr = new int[2];
			System.out.println(arr[3]);
		}
		catch (Exception e)
		{
			try
			{
				Date d = new Date();
				SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
				String s = sdf.format(d);

				PrintStream ps = new PrintStream("exception.log");
				//或者 ps.write(d.getBytes());
				ps.println(s);
				System.setOut(ps);
			}
			catch (IOException e)
			{
				throw new RuntimeException("xxxx");
			}
	
			e.printStackTrace();
		}
	}
	  
### log4j  专门处理日志工具  
  
	Properties prop = System.getProperties();
	prop.list(new PrintStream("sysinfo.txt"));//流没有关  
  
---  
  
## File类  
* 用来将文件或者文件夹封装成对象  
  
		public boolean createNewFile() throws IOException
如果该文件已经存在，则不创建，返回false
  
* 删除  
	* public boolean delete()    
		* 删除此抽象路径名表示的文件或目录。如果此路径名表示一个目录，则该目录必须为空才能删除。 
	* public void deleteOnExit()
		* 在虚拟机终止时，请求删除此抽象路径名表示的文件或目录。 文件（或目录）将以与注册相反的顺序删除。调用此方法删除已注册为删除的文件或目录无效。根据 Java 语言规范中的定义，只有在虚拟机正常终止时，才会尝试执行删除操作。 
例子  
	File f = new File("file.txt");
	f.deleteOnExit();   
  
