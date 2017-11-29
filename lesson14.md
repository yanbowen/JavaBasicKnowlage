# Java基础14  
<hr>     
  
https://www.cnblogs.com/lovebread/archive/2009/11/23/1609122.html  

## IO流  
  
* IO流用来处理设备之间的数据传输
* Java对数据的操作是通过流的方式
* 流按操作数据分为两种： 字节流与字符流 （字符流是基于字节流的，图片用字节流）
* 按照流向分为： 输入流，输出流  
  
* 字节流的抽象基类
	* InputStream
	* OutputStream
* 字符流的抽象基类
	* Reader
	* Writer  
	 
---
* 类 FileWriter
	* 用来写入字符文件的便捷类。此类的构造方法假定默认字符编码和默认字节缓冲区大小都是可接受的。
	
	* 注意IOException异常
	  
			//该文件会被创建到指定的目录下.如果有同名文件将会被覆盖
			//该步骤就是明确数据存放的目的地
			FileWriter fw = new FileWriter("demo.txt");
			//调用write方法，将字符串写入流中 
			fw.write("abdcd");
			//刷新流对象中的缓冲数据
			//将数据刷到目的地中
			fw.flush();
			
			//关闭流资源，但是关闭之前会刷新一次内部缓冲数据，刷新后，会将流关闭
			fw.close();  
  
---

	//创建文件并输入数据  
	FileWriter fw = null;
	try
	{
		fw = new FileWriter("demo.txt");
		fw.write("abdcdfd");
		
	}
	catch (IOException e)
	{
		
	}
	finally
	{
		try
		{
			if(fw!=null)
				fw.close();  
		}
		catch (IOException e)
		{
		
		}
	}
  
---
  
	public static void main(String[] args)
	{
		FileWriter fw = null;
		try
		{
			//传递一个true参数，代表不覆盖已有文件。并在已有文件的末尾处进行数据续写
			fw = new FileWriter("demo.txt",true);
			fw.write("jajja\r\nsddfe ");

		}
		catch (IOException e)
		{
			
		}
		finally
		{
			try
			{
				if(fw!=null)
					fw.close();
			}
			catch (IOException e)
			{
				
			}
		}
		
	}

---
  

* 类 Reader  
	* 用于读取字符流的抽象类。子类必须实现的方法只有 read(char[], int, int) 和 close()。但是，多数子类将重写此处定义的一些方法，以提供更高的效率和/或其他功能。
  
* 类 FileReader  
	* 用来读取字符文件的便捷类。此类的构造方法假定默认字符编码和默认字节缓冲区大小都是适当的。  
	  

			//创建一个文件读取流对象，和指定名称的文件相关联
			//保证该文件是已经存在的，若不存在，会报FileNotFoundException异常
			FileReader fr = new FileReader("demo.txt");

			//第一种方式
			//调用读取流read方法 //read()：一次读取一个字符，而且会自动往下读取
			
			//第二种方式
			  
  
---
    /**
     * 以字符为单位读取文件，常用于读文本，数字等类型的文件
     */
    public static void readFileByChars(String fileName) {
        FileReader fr = null;
		char[] tempchars = new char[1024];
		int charread = 0;
        try {
            reader = new FileReader("demo.txt"));
            while ((charread = fr.read(tempchars)) != -1) {
				System.out.print(new String(tempchars,0,charread));
            }
        } catch (Exception e1) {
            e1.printStackTrace();
        } finally {
            if (fr != null) {
                try {
                    fr.close();
                } catch (IOException e1) {
                }
            }
        }
    }  
   
---  
  
	public void  copy()
	{
		FileWriter fw = null;
		FileReader fr = null;
		try
		{
			fw = new FileWriter("source.txt");
			fr = new FileReader("destination.txt");
			
			char[] buf = new char[1024];
			int charread = 0;
			while((charread=fr.read(buf))!=-1)
			{
				fw.write(buf,0,charread);
			}
		}
		catch (IOException e)
		{
			throw new RuntimeException("读写失败")；
		}
		finally
		{
			if(fr!=null)
				try
				{
					fr.close();
				}
				catch (IOException e)
				{
					
				}
			if(fw!=null)
				try
				{
					fw.close();
				}
				catch (IOException e)
				{
					
				}
		}
	}  
  
---  
  
## 字符流和字节流

字符流的由来： 因为数据编码的不同，而有了对字符进行高效操作的流对象。本质其实就是基于字节流读取时，去查了指定的码表。 字节流和字符流的区别：  

读写单位不同：字节流以字节（8bit）为单位，字符流以字符为单位，根据码表映射字符，一次可能读多个字节。  
处理对象不同：字节流能处理所有类型的数据（如图片、avi等），而字符流只能处理字符类型的数据。  
结论：只要是处理纯文本数据，就优先考虑使用字符流。 除此之外都使用字节流。   
  
![](https://i.imgur.com/AyjKZFA.png)